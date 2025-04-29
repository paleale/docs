# Data delivery in ksqlDB

ksqlDB is a database designed for stream processing messages from {{ KF }} topics. Working with message streams in ksqlDB is similar to working with tables in a regular database. The ksqlDB table is automatically updated with data from a topic, and the data that you add to the ksqlDB table is sent to the {{ KF }} topic. You can learn more in the [ksqlDB documentation](https://docs.ksqldb.io/en/latest).

To set up data delivery from {{ mkf-name }} to ksqlDB:
1. [Set up {{ KF }} integration for the ksqlDB database](#configure-ksqldb-for-kf).
1. [Review the format of the data coming from {{ mkf-name }}](#explore-kf-data-format).
1. [Create a table in ksqlDB to capture the data stream from the {{ KF }} topic](#create-kf-table).
1. [Get test data from the {{ mkf-name }} cluster](#get-data-from-kf).
1. [Write the test data to ksqlDB](#insert-data-to-ksqldb).
1. [Check that the test data is present in the {{ KF }} topic](#fetch-data-from-kf).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mkf-name }} cluster fee: Using computing resources allocated to hosts (including {{ ZK }} hosts) and disk space (see [{{ KF }} pricing](../../managed-kafka/pricing.md)).
* Fee for using public IP addresses if public access is enabled for cluster hosts (see [{{ vpc-name }} pricing](../../vpc/pricing.md)).


## Getting started {#before-you-begin}

1. [Create a {{ mkf-name }}](../../managed-kafka/operations/cluster-create.md) cluster in any suitable configuration.

    * If the ksqlDB server is hosted on the internet, create a {{ mkf-name }} cluster with public access.
    
    * If the ksqlDB server is hosted in {{ yandex-cloud }}, create a {{ mkf-name }} cluster on the same [cloud network](../../vpc/concepts/network.md) as ksqlDB.


1. [Create topics](../../managed-kafka/operations/cluster-topics.md#create-topic) in a {{ mkf-name }} cluster:
   1. Service topic named `_confluent-ksql-default__command_topic` set up as follows:
        * Replication factor: `1`
        * Number of partitions: `1`
        * Log cleanup policy: `Delete`
        * Log segment lifetime, ms: `-1`
        * Minimum number of in-sync replicas: `1`
   1. Service topic named `default_ksql_processing_log` to write ksqlDB logs to. Use any settings.
   1. Data storage topic named `locations`. Use any settings.

1. [Create a user](../../managed-kafka/operations/cluster-accounts.md#create-account) named `ksql` and [assign them the `ACCESS_ROLE_ADMIN` role](../../managed-kafka/operations/cluster-accounts.md#grant-permission) for all topics.

1. Check that you can connect to the ksqlDB server.

1. Install the `kafkacat` utility on the ksqlDB server and check that you can use it to [connect to a {{ mkf-name }} cluster over SSL](../../managed-kafka/operations/connect/clients.md#bash-zsh).

1. Install the [jq](https://stedolan.github.io/jq/) utility for stream processing JSON files on the ksqlDB server.

## Set up {{ KF }} integration for the ksqlDB database {#configure-ksqldb-for-kf}

1. Connect to the ksqlDB server.
1. Add the server's SSL certificate to the Java trusted certificate store (Java Key Store) so that ksqlDB can use this certificate for secure connections to the cluster hosts. Set a password in the `-storepass` parameter for additional storage protection:

   ```bash
   cd /etc/ksqldb && \
   sudo keytool -importcert -alias {{ crt-alias }} -file {{ crt-local-dir }}{{ crt-local-file }} \
   -keystore ssl -storepass <certificate_store_password> \
   --noprompt
   ```

1. In the `/etc/ksqldb/ksql-server.properties` ksqlDB configuration file, specify the credentials for authentication in the {{ mkf-name }} cluster:

   ```ini
   bootstrap.servers=<broker_FQDN_1>:9091,...,<broker_FQDN_N>:9091
   sasl.mechanism=SCRAM-SHA-512
   security.protocol=SASL_SSL
   ssl.truststore.location=/etc/ksqldb/ssl
   ssl.truststore.password=<certificate_store_password>
   sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="ksql" password="<ksql_user_password>";
   ```

   {% include [fqdn](../../_includes/mdb/mkf/fqdn-host.md) %}

   You can request the cluster name with the [list of clusters in the folder](../../managed-kafka/operations/cluster-list.md#list-clusters).

1. In the ksqlDB logging configuration file named `/etc/ksqldb/log4j.properties`, configure logging to a {{ mkf-name }} cluster topic:

   ```ini
   log4j.appender.kafka_appender=org.apache.kafka.log4jappender.KafkaLog4jAppender
   log4j.appender.kafka_appender.layout=io.confluent.common.logging.log4j.StructuredJsonLayout
   log4j.appender.kafka_appender.BrokerList=<broker_FQDN_1>:9091,...,<broker_FQDN_N>:9091
   log4j.appender.kafka_appender.Topic=default_ksql_processing_log
   log4j.logger.io.confluent.ksql=INFO,kafka_appender

   log4j.appender.kafka_appender.clientJaasConf=org.apache.kafka.common.security.scram.ScramLoginModule required username="ksql" password="<ksql_user_password>";
   log4j.appender.kafka_appender.SecurityProtocol=SASL_SSL
   log4j.appender.kafka_appender.SaslMechanism=SCRAM-SHA-512
   log4j.appender.kafka_appender.SslTruststoreLocation=/etc/ksqldb/ssl
   log4j.appender.kafka_appender.SslTruststorePassword=<certificate_store_password>
   ```

1. Restart the ksqlDB service with the command below:

    ```bash
    sudo systemctl restart confluent-ksqldb.service
    ````

## Review the format of the data coming from {{ mkf-name }} {#explore-kf-data-format}

The processing of the {{ mkf-name }} data stream depends on the {{ KF }} message view format.

In the example, geodata is written to the `locations` {{ KF }} topic in JSON format:

* `profileId`: ID
* `latitude`: Latitude
* `longitude`: Longitude

This data will be transmitted as {{ KF }} messages. Each message will contain a JSON object as a string in the following format:

```json
{"profileId": "c2309eec", "latitude": 37.7877, "longitude": -122.4205}
```

ksqlDB stores the values of the corresponding parameters from {{ KF }} messages in a three-column table. 

Next, we are going to configure the fields of a ksqlDB data stream table.

## Create a table in ksqlDB to capture the data stream from the {{ KF }} topic {#create-kf-table}

Create a table in ksqlDB for writing data from the {{ KF }} topic. The table structure matches the [format of the data](#explore-kf-data-format) from {{ mkf-name }}:

1. Connect to the ksqlDB server.
1. Run the `ksql` client using this command:

   ```bash
   ksql http://0.0.0.0:8088
   ```

1. Run this request:

   ```sql
   CREATE STREAM riderLocations 
   (
     profileId VARCHAR,
     latitude DOUBLE,
     longitude DOUBLE
   ) WITH 
   (
     kafka_topic='locations', 
     value_format='json', 
     partitions=<number_of_"locations”_topic_sections>
   );
   ```

   This data stream table will be automatically populated with messages from the {{ mkf-name }} cluster's `locations` topic. ksqlDB uses the `ksql` user's [settings](#configure-ksqldb-for-kf) to read messages.

   For more information about creating a data stream table in the ksqlDB engine, see the [ksqlDB documentation](https://www.confluent.io/blog/how-real-time-stream-processing-works-with-ksqldb).

1. Run this request:

   ```sql
   SELECT * FROM riderLocations WHERE 
            GEO_DISTANCE(latitude, longitude, 37.4133, -122.1162) <= 5 
            EMIT CHANGES;
   ```

   The query waits for real-time data to appear in the table.

## Get test data from the {{ mkf-name }} cluster {#get-data-from-kf}

1. Connect to the ksqlDB server.
1. Create a file named `sample.json` with the following test data:

   ```json
   {
     "profileId": "c2309eec", 
     "latitude": 37.7877,
     "longitude": -122.4205
   }

   {
     "profileId": "4ab5cbad", 
     "latitude": 37.3952,
     "longitude": -122.0813
   }

   {
     "profileId": "4a7c7b41", 
     "latitude": 37.4049,
     "longitude": -122.0822
   }   
   ```

1. Send a file named `sample.json` to the {{ mkf-name }} cluster's `locations` topic using `jq` and `kafkacat`:

   ```bash
   jq -rc . sample.json | kafkacat -P \
      -b <broker_FQDN_1>:9091,...,<broker_FQDN_N>:9091> \
      -t locations \
      -X security.protocol=SASL_SSL \
      -X sasl.mechanisms=SCRAM-SHA-512 \
      -X sasl.username=ksql \
      -X sasl.password="<ksql_user_password>" \
      -X ssl.ca.location={{ crt-local-dir }}{{ crt-local-file }} -Z
   ```

   The information is sent using the `ksql` user. To learn more about setting up an SSL certificate and working with `kafkacat`, see [{#T}](../../managed-kafka/operations/connect/clients.md).

1. Make sure that the [session](#create-kf-table) displays the data sent to the topic:

   ```text
   +--------------------------+--------------------------+------------------------+
   |PROFILEID                 |LATITUDE                  |LONGITUDE               |
   +--------------------------+--------------------------+------------------------+
   |4ab5cbad                  |37.3952                   |-122.0813               | 
   |4a7c7b41                  |37.4049                   |-122.0822               |
   ```

  The data is read using the `ksql` user.

## Write the test data to ksqlDB {#insert-data-to-ksqldb}

1. Connect to the ksqlDB server.
1. Run the `ksql` client using this command:

   ```bash
   ksql http://0.0.0.0:8088
   ```

1. Insert the test data into the `riderLocations` table:

   ```sql
   INSERT INTO riderLocations (profileId, latitude, longitude) VALUES ('18f4ea86', 37.3903, -122.0643);
   INSERT INTO riderLocations (profileId, latitude, longitude) VALUES ('8b6eae59', 37.3944, -122.0813);
   INSERT INTO riderLocations (profileId, latitude, longitude) VALUES ('4ddad000', 37.7857, -122.4011);
   ```

   This data is sent synchronously to the `locations` {{ KF }} topic using the `ksql` user.

## Check for records in {{ KF }} topics {#fetch-data-from-kf}

1. Check messages in the {{ mkf-name }} cluster's `locations` topic using `kafkacat` and the `ksql` user:

   ```bash
   kafkacat -C \
    -b <broker_FQDN_1>:9091,...,<broker_FQDN_N>:9091 \
    -t locations \
    -X security.protocol=SASL_SSL \
    -X sasl.mechanisms=SCRAM-SHA-512 \
    -X sasl.username=ksql \
    -X sasl.password="<ksql_user_password>" \
    -X ssl.ca.location={{ crt-local-dir }}{{ crt-local-file }} -Z -K:
   ```

1. Make sure that the console displays the messages you [inserted into the table](#insert-data-to-ksqldb).
1. Check messages in the {{ mkf-name }} cluster's `default_ksql_processing_log` topic using `kafkacat` and the `ksql` user:

   ```bash
   kafkacat -C \
    -b <broker_FQDN_1>:9091,...,<broker_FQDN_N>:9091 \
    -t default_ksql_processing_log \
    -X security.protocol=SASL_SSL \
    -X sasl.mechanisms=SCRAM-SHA-512 \
    -X sasl.username=ksql \
    -X sasl.password="<ksql_user_password>" \
    -X ssl.ca.location={{ crt-local-dir }}{{ crt-local-file }} -Z -K:
   ```

1. Make sure the console displays ksqlDB log records.

## Delete the resources you created {#clear-out}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

* [Delete the virtual machine](../../compute/operations/vm-control/vm-delete.md).
* If you reserved a public static IP for your virtual machine, [delete it](../../vpc/operations/address-delete.md).
* [Delete the {{ mkf-name }} cluster](../../managed-kafka/operations/cluster-delete.md).
