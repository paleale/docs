# Working with {{ iot-full-name }} in Java

In this scenario, you will learn how to connect to {{ iot-full-name }} using the [Paho](https://www.eclipse.org/paho/clients/java/) library written in Java. It assumes that you know how to develop in Java.

{% note info %}

The source code used in this scenario is available on [GitHub](https://github.com/yandex-cloud-examples/yc-iot-android).

{% endnote %}

Once connected, you can:

* [Send messages](../../iot-core/operations/publish.md).
* [Subscribe a device or registry to receive messages](../../iot-core/operations/subscribe.md).

To connect to {{ iot-full-name }} and start messaging:

* [Create the required {{ iot-full-name }}](#resources) resources:
  * [Create a registry and add a certificate to it](#registry).
  * [Create a device and add a certificate to it](#device).
* [Connect to {{ iot-full-name }}](#configure).
* [Authenticate in {{ iot-full-name }}](#auth):
  * [Authentication using certificates](#certs).
  * [Authentication by username and password](#log-pass).
* [Establish a connection](#connect).
* [Subscribe to a topic and receive messages](#subscribe).
* [Send a message](#publish).
* [Terminate the connection](#disconnect).

## Getting started {#before-you-begin}

1. If you do not have the {{ yandex-cloud }} CLI yet, [install and initialize it](../../cli/quickstart.md#install).
1. Download and install [Java Development Kit](https://www.oracle.com/java/technologies/javase-downloads.html).

## Create the required {{ iot-full-name }} resources {#resources}

### Create a registry and add a certificate to it {#registry}

If you already have a certificate, start with the second step.

1. Create a certificate for the registry:

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

1. Create a certificate for the device:

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

## Connect to {{ iot-full-name }} {#configure}

Before connecting, configure the `connOpts` connection parameters using the following code:

```java
String clientId = "YandexIoTCoreTestJavaClient";
int keepAliveInterval = 60;
MqttClient client = new MqttClient("ssl://mqtt.cloud.yandex.net:8883", clientId);

// Installing an asynchronous event handler
client.setCallback(listener);

// Setting up the connection parameters.
MqttConnectOptions connOpts = new MqttConnectOptions();
connOpts.setKeepAliveInterval(keepAliveInterval);

```

Where:

* `MqttClient`: Class that specifies the {{ iot-full-name }} connection parameters:
    * Address and port
    * Client ID
* `MqttConnectOptions`: Class that sets the connection options. You can use the default settings, but we recommend setting the `keepAliveInterval` parameter. Its value determines the frequency of sending `PINGREQ` commands. The lower the `keepAliveInterval` value, the sooner the client will realize that the connection was terminated abnormally. However, this increases the frequency of billable `PINGREQ` commands.
* `listener`: Class that implements the `MqttCallback` interface. It is used to process such events as server connection loss (`connectionLost`), message delivery (`deliveryComplete`), and new message arrival (`messageArrived`).

## Get authenticated in {{ iot-full-name }} {#auth}

There are two [authentication](../../iot-core/concepts/authorization.md) methods:
* [Using X.509 certificates](#certs).
* [Using a username and password](#log-pass).

### Authentication using certificates {#certs}

When authenticating with X.509 certificates, use [PKCS#12](https://ru.wikipedia.org/wiki/PKCS12) certificates (.p12 files). To generate this certificate from PEM certificates, run the command:

```bash
openssl pkcs12 -export -in cert.pem -inkey key.pem -out keystore.p12
```

#### File structure {#file-structure}

In the example available on [GitHub](https://github.com/yandex-cloud-examples/yc-iot-android), certificates are loaded from files with the following structure:

```text
  /my_registry        Registry directory |current directory|. The example should be run from this directory.
  `- /device          Device directory |device|.
  |  `- cert.pem      Device certificate in PEM format.
  |  `- key.pem      Device key in PEM format.
  |  `- keystore.p12  Device certificate and key in PKCS#12 format.
  `- cert.pem        Registry certificate in PEM format.
  `- key.pem          Registry key in PEM format.
  `- keystore.p12    Registry certificate and key in PKCS#12 format.
```

#### Loading certificates {#cert-load}

The certificate authority (CA) certificate is set as a static variable named `TRUSTED_ROOT`.

The example uses the following certificate loading method:

```java
private SSLSocketFactory getSocketFactoryWithCerts(String certsDir)
```

Certificates are loaded in several stages:

1. Load the certificate used for server authentication:

    ```java
    // Loading the CA certificate from the `TRUSTED_ROOT` static variable.
    InputStream is = new ByteArrayInputStream(TRUSTED_ROOT.getBytes(StandardCharsets.UTF_8));
    CertificateFactory cFactory = CertificateFactory.getInstance("X.509");
    X509Certificate caCert = (X509Certificate) cFactory.generateCertificate(is);

    // Using the CA certificate for server authentication.
    KeyStore tks = KeyStore.getInstance(KeyStore.getDefaultType());
    tks.load(null);
    tks.setCertificateEntry("caCert", caCert);
    TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
    tmf.init(tks);
    ```

1. Load the client certificate used for authentication on the server from the `keystore.p12` file:

    ```java
    final char[] emptyPassword = "".toCharArray();
    KeyStore ks = KeyStore.getInstance("PKCS12");
    ks.load(new FileInputStream(Paths.get(certsDir, "keystore.p12").toString()), emptyPassword);
    KeyManagerFactory kmf = KeyManagerFactory.getInstance("SunX509");
    kmf.init(ks, emptyPassword);
    ```

1. Get an instance of the `SSLSocketFactory` class:

    ```java
    SSLContext ctx = SSLContext.getInstance("TLS");
    ctx.init(kmf.getKeyManagers(), tmf.getTrustManagers(), null);
    SSLSocketFactory sslSocketFactory = ctx.getSocketFactory();
    ```

After that, provide the obtained `sslSocketFactory` instance to the connection parameters:

```java
connOpts.setSocketFactory(sslSocketFactory);
```

### Authenticating by username and password {#log-pass}

Since authentication using a username and password in {{ iot-full-name }} requires the TLS protocol, you need to load the certificate used for server authentication:

```java
// Loading the CA certificate from the `TRUSTED_ROOT` static variable.
private SSLSocketFactory getSocketFactory()
                throws Exception {
    InputStream is = new ByteArrayInputStream(TRUSTED_ROOT.getBytes(StandardCharsets.UTF_8));
    CertificateFactory cFactory = CertificateFactory.getInstance("X.509");
    X509Certificate caCert = (X509Certificate) cFactory.generateCertificate(
            is);

    TrustManagerFactory tmf = TrustManagerFactory
            .getInstance(TrustManagerFactory.getDefaultAlgorithm());
    KeyStore tks = KeyStore.getInstance(KeyStore.getDefaultType());
    tks.load(null); // You do not need to load a `KeyStore` class instance from the file.
    tks.setCertificateEntry("caCert", caCert);
    tmf.init(tks);

    SSLContext ctx = SSLContext.getInstance("TLS");
    ctx.init(null, tmf.getTrustManagers(), null);
    return ctx.getSocketFactory();
}
```

In the connection settings, specify the username (registry or device ID) and password:

```java
connOpts.setUserName(login.trim());
connOpts.setPassword(password.trim().toCharArray());
```

and `sslSocketFactory` you can get from the code above:

```java
SSLSocketFactory sslSocketFactory = getSocketFactory();
connOpts.setSocketFactory(sslSocketFactory);
```

## Establish a connection {#connect}

Establish a connection to {{ iot-full-name }} using the following method:

```java
client.connect(connOpts);
```

The `connect` method is a blocking one. It blocks until the connection is established.

To handle a server connection loss event, you can use the `connectionLost` method of the `MqttCallback` interface:

```java
@Override
public void connectionLost(Throwable cause) {
}
```

In which case you should install the event handler (using the `setCallback` method) prior to invoking the `connect` method:

```java
client.setCallback(listener);
...
client.connect(connOpts);
...
```

Where `listener` is the class that implements the `MqttCallback` interface.

## Subscribe to a topic and get messages {#subscribe}

Subscribe to a topic using the following code. In the `subscribe` method, specify the topic (`topic`) you want to subscribe to and the QoS level (`qos`).

```java
client.subscribe(topic, qos);
```

To handle new message arrival events, you can use the `messageArrived` method of the `MqttCallback` interface:

```java
@Override
public void messageArrived(String topic, MqttMessage message){
}
```

In which case you should install the event handler (using the `setCallback` method) prior to invoking the `connect` method:

```java
client.setCallback(listener);
...
client.connect(connOpts);
...
```

Where `listener` is the class that implements the `MqttCallback` interface.

You can also use the `messageArrived` method of the `IMqttMessageListener` interface to handle new message arrival events:

```java
client.subscribe(topic, qos, messageListener);
```

Where `messageListener` is the class that implements the `IMqttMessageListener` interface.

## Send a message {#publish}

Send a message using the following code. In the `publish` method, specify the `topic` you want to send a message to and the message text using the `MqttMessage` class. You can also specify the desired `qos` level for an instance of the `MqttMessage` class, if needed.

```java
MqttMessage msg = new MqttMessage(message);
msg.setQos(qos);
client.publish(topic, msg);
```

To handle a message delivery event, you can use the `deliveryComplete` method of the `MqttCallback` interface:

```java
@Override
public void deliveryComplete(IMqttDeliveryToken token) {
}
```

In which case you should install the event handler (using the `setCallback` method) prior to invoking the `connect` method:

```java
client.setCallback(listener);
...
client.connect(connOpts);
...
```

Where `listener` is the class that implements the `MqttCallback` interface.

## Terminate the connection {#disconnect}

Disconnect from {{ iot-full-name }} using the following methods:

```java
client.disconnect();
client.close();
```

Where:

* The `disconnect` method closes the connection to the server.
* The `close` method releases the `MqttClient` class resources.
