# Working with {{ iot-full-name }} in C#

In this tutorial, you will learn how to connect to {{ iot-full-name }} using the [Paho](https://www.eclipse.org/paho/clients/dotnet/) library written in C#. The tutorial assumes that you know how to develop apps on .NET in C\#.

{% note info %}

For the source code discussed in the tutorial, visit [GitHub](https://github.com/yandex-cloud-examples/yc-mqtt-server-iot). Interactions with {{ iot-full-name }} are encapsulated in the [YaClient](https://github.com/yandex-cloud-examples/yc-mqtt-server-iot/blob/main/YaClient.cs) class that you can use in your projects.

{% endnote %}

Once connected, you can:

* [Send messages](../../iot-core/operations/publish.md).
* [Subscribe a device or registry to receive messages](../../iot-core/operations/subscribe.md).

To connect to {{ iot-full-name }} and start messaging:

* [Create the required {{ iot-full-name }}](#resources) resources:
  * [Create a registry and add a certificate to it](#registry).
  * [Create a device and add a certificate to it](#device).
* [Connect to {{ iot-full-name }}](#connect).
* [Authenticate in {{ iot-full-name }}](#auth):
  * [Authentication using certificates](#certs).
  * [Authentication by username and password](#log-pass).
* [Establish a connection](#establish-connection).
* [Subscribe to a topic and receive messages](#subscribe).
* [Send a message](#publish).
* [Terminate the connection](#disconnect).

## Getting started {#before-you-begin}

1. If you do not have the {{ yandex-cloud }} CLI yet, [install and initialize it](../../cli/quickstart.md#install).
1. Download and install a development environment for .Net, e.g., [Microsoft Visual Studio](https://visualstudio.microsoft.com/ru/vs/).

## Create the required {{ iot-full-name }} resources {#resources}

### Create a registry and add a certificate to it {#registry}

If you already have a certificate, start with the second step.

1. Create a certificate for the registry (skip this step if you already have a registry certificate):

   ```bash
   openssl req -x509 \
     -newkey rsa:4096 \
     -keyout registry-key.pem \
     -out registry-cert.pem \
     -nodes \
     -days 365 \
     -subj '/CN=localhost'
   ```

1. Create a registry:

   ```bash
   yc iot registry create --name my-registry
   ```

1. Add a certificate to the registry:

   ```bash
   yc iot registry certificate add \
     --registry-name my-registry \ # Registry name.
     --certificate-file registry-cert.pem # Path to the public part of the certificate.
   ```

### Create a device and add a certificate to it {#device}

If you already have a certificate, start with the second step.

1. (Optional) Create a certificate for the device:

   ```bash
   openssl req -x509 \
     -newkey rsa:4096 \
     -keyout device-key.pem \
     -out device-cert.pem \
     -nodes \
     -days 365 \
     -subj '/CN=localhost'
   ```

1. [Review a list of the registries](../../iot-core/operations/registry/registry-list.md#registry-list) where you can create a device or [create a new registry](../../iot-core/operations/registry/registry-create.md).
1. Create a device:

   ```bash
   yc iot device create --registry-name my-registry --name my-device
   ```

1. Add a certificate to the device:

   ```bash
   yc iot device certificate add \
     --device-name my-device \ # Device name.
     --certificate-file device-cert.pem # Path to the public part of the certificate.
   ```

## Connect to {{ iot-full-name }} {#connect}

Before connecting, configure the connection parameters using the following code:

```C#
// Setting up a TLS connection
MqttClientOptionsBuilderTlsParameters tlsOptions = new MqttClientOptionsBuilderTlsParameters
{
  SslProtocol = SslProtocols.Tls12,
  UseTls = true
};

// Enabling the server validation event handler
tlsOptions.CertificateValidationCallback += CertificateValidationCallback;

// Setting up the connection parameters
var options = new MqttClientOptionsBuilder()
    .WithClientId($"Test_C#_Client_{Guid.NewGuid()}")
    .WithTcpServer(MqttServer, MqttPort)
    .WithTls(tlsOptions)
    .WithCleanSession()
    .WithCredentials(id, password)
    .WithKeepAlivePeriod(TimeSpan.FromSeconds(90))
    .WithKeepAliveSendInterval(TimeSpan.FromSeconds(60))
    .Build();

var factory = new MqttFactory();
IMqttClient mqttClient = factory.CreateMqttClient();

// Enabling the data receipt event handler
mqttClient.UseApplicationMessageReceivedHandler(DataHandler);

// Enabling the event handler for connections to {{ iot-full-name }}
mqttClient.UseConnectedHandler(ConnectedHandler);

// Enabling the event handler for losing connections to {{ iot-full-name }}
mqttClient.UseDisconnectedHandler(DisconnectedHandler);
```

Where:

* `MqttClientOptionsBuilderTlsParameters`: Class that specifies the TLS usage parameters when connecting to {{ iot-full-name }}.
* `MqttClientOptionsBuilder`: Constructor class that sets the connection options. You can use the default settings, but we recommend setting the `WithKeepAlivePeriod` and `WithKeepAliveSendInterval` parameters. The values of these parameters determine:
    * Rate of sending the `PINGREQ` commands.
    * Client response time to a broken connection.

        The lower the above parameters, the sooner the client will find out that the connection was terminated abnormally. However, this increases the frequency of billable `PINGREQ` commands.

## Get authenticated in {{ iot-full-name }} {#auth}

There are two [authentication](../../iot-core/concepts/authorization.md) methods:
* [Using X.509 certificates](#certs).
* [Using a username and password](#log-pass).

### Authentication using certificates {#certs}

When authenticating with X.509 certificates, it is most convenient to use [PKCS#12](https://ru.wikipedia.org/wiki/PKCS12) certificates in PFX format. To generate a certificate in PKCS#12 format from PEM certificates, run the command:

```bash
openssl pkcs12 -export -out cert.pfx -inkey key.pem -in cert.pem
```

To load certificates in your project, use the following code:

```C#
X509Certificate2 certificate = new X509Certificate2(certPath);
```

The client certificate used for authentication on the server is specified when setting up a TLS connection:

```C#
X509Certificate2 certificate = new X509Certificate2(certPath);
List<byte[]> certificates = new List<byte[]>();
certificates.Add(certificate.Export(X509ContentType.SerializedCert));

// Setting up a TLS connection
MqttClientOptionsBuilderTlsParameters tlsOptions = new MqttClientOptionsBuilderTlsParameters
{
  Certificates = certificates,
  SslProtocol = SslProtocols.Tls12,
  UseTls = true
};
```

The certificate used for server authentication is used in the server validation event handler:

```C#
private static X509Certificate2 rootCrt = new X509Certificate2("rootCA.crt");

private static bool CertificateValidationCallback(X509Certificate cert, X509Chain chain, SslPolicyErrors errors, IMqttClientOptions opts)
{
  try
  {
    if (errors == SslPolicyErrors.None)
    {
      return true;
    }

    if (errors == SslPolicyErrors.RemoteCertificateChainErrors)
    {
      chain.ChainPolicy.RevocationMode = X509RevocationMode.NoCheck;
      chain.ChainPolicy.VerificationFlags = X509VerificationFlags.NoFlag;
      chain.ChainPolicy.ExtraStore.Add(rootCrt);

      chain.Build((X509Certificate2)rootCrt);
      // Comparing certificate thumbprints
      var res = chain.ChainElements.Cast<X509ChainElement>().Any(a => a.Certificate.Thumbprint == rootCrt.Thumbprint);
      return res;
    }
  }
  catch { }

  return false;
}
```

### Authenticating by username and password {#log-pass}

Authentication with a username and password in {{ iot-full-name }} requires the TLS protocol. To do this, use the `MqttClientOptionsBuilderTlsParameters` class:

```C#
// Setting up a TLS connection
MqttClientOptionsBuilderTlsParameters tlsOptions = new MqttClientOptionsBuilderTlsParameters
{
  SslProtocol = SslProtocols.Tls12,
  UseTls = true
};

// Enabling the server validation event handler
tlsOptions.CertificateValidationCallback += CertificateValidationCallback;
```

In the connection settings, use the `WithCredentials` method to specify the username (registry or device `id`) and `password`:

```C#
// Setting up the connection parameters
var options = new MqttClientOptionsBuilder()
    .WithClientId($"Test_C#_Client_{Guid.NewGuid()}")
    .WithTcpServer(MqttServer, MqttPort)
    .WithTls(tlsOptions)
    .WithCleanSession()
    .WithCredentials(id, password)
    .WithKeepAlivePeriod(TimeSpan.FromSeconds(90))
    .WithKeepAliveSendInterval(TimeSpan.FromSeconds(60))
    .Build();
```

## Establish a connection {#establish-connection}

Establish a connection to {{ iot-full-name }} using the following code:

```C#
mqttClient.ConnectAsync(options, CancellationToken.None);
```

## Subscribe to a topic and get messages {#subscribe}

Use a callback function to receive messages:

```C#
...
  mqttClient.UseApplicationMessageReceivedHandler(DataHandler);
...

private Task DataHandler(MqttApplicationMessageReceivedEventArgs arg)
{
  return Task.CompletedTask;
}
```

Subscribe to a topic using the following code. In the `SubscribeAsync` method, specify the topic (`topic`) you want to subscribe to and the QoS level (`qos`).

```C#
mqttClient.SubscribeAsync(topic, qos);
```

## Send a message {#publish}

Send a message using the following code. In the `PublishAsync` method, specify the `topic` you want to send a message to, the message text, and the preferred quality of service level (`qos`).

```C#
mqttClient.PublishAsync(topic, "Your message text.", qos);
```

Handle connection loss events and track message delivery using callback functions:

```C#
...
  mqttClient.UseConnectedHandler(ConnectedHandler);
  mqttClient.UseDisconnectedHandler(DisconnectedHandler);
...

private Task ConnectedHandler(MqttClientConnectedEventArgs arg)
{
  return Task.CompletedTask;
}

private static Task DisconnectedHandler(MqttClientDisconnectedEventArgs arg)
{
  return Task.CompletedTask;
}
```

## Terminate the connection {#disconnect}

Disconnect from {{ iot-full-name }} using the following code:

```C#
mqttClient.DisconnectAsync();
```
