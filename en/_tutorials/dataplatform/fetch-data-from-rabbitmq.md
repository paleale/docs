# Fetching data from {{ RMQ }} to {{ mch-full-name }}


You can supply data from {{ RMQ }} to a {{ mch-name }} cluster in real time. {{ mch-name }} will automatically insert the data routed into particular exchange points of the specified {{ RMQ }} queues into a [{{ RMQ }}]({{ ch.docs }}/engines/table-engines/integrations/rabbitmq/) table.

To set up data delivery from {{ RMQ }} to {{ mch-name }}:

1. [Set up integration with {{ RMQ }} for the {{ mch-name }} cluster](#configure-mch-for-rmq).
1. [In the {{ mch-name }} cluster, create a {{ RMQ }} table](#create-rmq-table).
1. [Send the test data to the {{ RMQ }} queue](#send-sample-data-to-rmq).
1. [Check that the test data is present in the {{ mch-name }} cluster table](#fetch-sample-data).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mch-name }} cluster fee: Using computing resources allocated to hosts (including {{ ZK }} hosts) and disk space (see [{{ mch-name } pricing}](../../managed-clickhouse/pricing.md)).
* Fee for using public IP addresses if public access is enabled for cluster hosts (see [{{ vpc-name }} pricing](../../vpc/pricing.md)).
* VM fee: using computing resources, storage, and, optionally, public IP address (see [{{ compute-name }} pricing](../../compute/pricing.md)).


## Getting started {#before-you-begin}

### Prepare the infrastructure {#deploy-infrastructure}

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a {{ mch-name }} cluster](../../managed-clickhouse/operations/cluster-create.md) in any suitable configuration with the `db1` database. To connect to the cluster from the user's local machine instead of the {{ yandex-cloud }} cloud network, enable public access to the cluster hosts when creating it.

        {% note info %}

        You can set up {{ RMQ }} integration when creating a cluster. In this article, integration will be configured [later](#configure-mch-for-rmq).

        {% endnote %}

    1. [Create a virtual machine](../../compute/operations/vm-create/create-linux-vm.md)for{{ RMQ }}. To connect to the cluster from the user's local machine rather than doing so from the {{ yandex-cloud }} network, enable public access when creating it.

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. Download the [clickhouse-cluster-and-vm-for-rabbitmq.tf](https://github.com/yandex-cloud-examples/yc-clickhouse-fetch-data-from-rabbitmq/blob/main/clickhouse-cluster-and-vm-for-rabbitmq.tf) configuration file to the same working directory.

        This file describes:

        * Network.
        * Subnet.
        * Default security group and rules required to connect to the cluster and VM from the internet.
        * {{ mch-name }} cluster.
        * Virtual machine.

    1. Specify the following in the `clickhouse-cluster-and-vm-for-rabbitmq.tf` file:

        * Username and password that will be used to access the {{ mch-name }} cluster.
        * ID of the public [Ubuntu](/marketplace?tab=software&search=Ubuntu&categories=os) [image](../../compute/operations/images-with-pre-installed-software/get-list.md) without GPU for the VM.
        * Username and path to the [public key](../../compute/operations/vm-connect/ssh.md#creating-ssh-keys) file for accessing the virtual machine. By default, the specified username is ignored in the image that is currently used. A user with the `ubuntu` username is created instead. Use it to connect to the VM.

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

### Configure additional settings {#additional-settings}

1. [Connect](../../compute/operations/vm-connect/ssh.md) to a virtual machine over SSH.

    1. Install {{ RMQ }}:

        ```bash
        sudo apt update && sudo apt install rabbitmq-server --yes
        ```

    1. Create a user for {{ RMQ }}:

        ```bash
        sudo rabbitmqctl add_user <username> <password>
        ```

    1. Grant this user permissions to connect to the server:

        ```bash
        sudo rabbitmqctl set_permissions -p / <username> ".*" ".*" ".*" && \
        sudo rabbitmqctl set_topic_permissions -p / <username> amq.topic "cars" "cars"
        ```

1. Install the `amqp-publish` and `amqp-declare-queue` utilities to work with {{ RMQ }} and [jq](https://stedolan.github.io/jq/) for stream processing JSON files:

    ```bash
    sudo apt update && sudo apt install amqp-tools --yes && sudo apt-get install jq --yes
    ```

1. Check if you can create a queue named `cars` in {{ RMQ }} using `amqp-declare-queue`:

    ```bash
    amqp-declare-queue \
        --url=amqp://<username>:<password>@<IP_address_or_FQDN_of_the_RabbitMQ_server>:5672 \
        --queue=cars
    ```

1. Install the `clickhouse-client` utility to connect to the database in the {{ mch-name }} cluster.

    1. Connect the [DEB repository]({{ ch.docs }}/getting-started/install/#install-from-deb-packages) {{ CH }}:

        ```bash
        sudo apt update && sudo apt install --yes apt-transport-https ca-certificates dirmngr && \
        sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4 && \
        echo "deb https://packages.{{ ch-domain }}/deb stable main" | sudo tee \
        /etc/apt/sources.list.d/clickhouse.list
        ```

    1. Install the dependencies:

        ```bash
        sudo apt update && sudo apt install clickhouse-client --yes
        ```

    1. Download the configuration file for `clickhouse-client`:

        {% include [ClickHouse client config](../../_includes/mdb/mch/client-config.md) %}

    Check that you can use `clickhouse-client` [to connect to the {{ mch-name }} cluster over SSL](../../managed-clickhouse/operations/connect/clients.md#clickhouse-client).

## Set up integration with {{ RMQ }} for the {{ mch-name }} cluster {#configure-mch-for-rmq}

{% list tabs group=instructions %}

- Manually {#manual}

    In the [{{ mch-name }} cluster settings](../../managed-clickhouse/operations/change-server-level-settings.md#yandex-cloud-interfaces), specify the username and password for {{ RMQ }} authentication in **{{ ui-key.yacloud.mdb.forms.section_settings }}** → **Rabbitmq**.

- {{ TF }} {#tf}

    Add the `clickhouse.config.rabbitmq` block with the username and password for {{ RMQ }} authentication to the cluster description:

    ```hcl
    resource "yandex_mdb_clickhouse_cluster" "clickhouse-cluster" {
      ...
      clickhouse {
        ...
        config {
          rabbitmq {
            username = "<username>"
            password = "<password>"
          }
        }
        ...
      }
    }
    ```

{% endlist %}

## In the {{ mch-name }} cluster, create a {{ RMQ }} table {#create-rmq-table}

Let's assume you put the following JSON car sensor data into the {{ RMQ }} queue named `cars` at the exchange point named `exchange`:

* `device_id`: String ID of the device.
* `datetime`: Date and time when the data was generated, in `YYYY-MM-DD HH:MM:SS` format.
* Car coordinates:

    * `latitude`: Latitude.
    * `longitude`: Longitude.
    * `altitude`: Altitude above sea level.

* `speed`: Current speed.
* `battery_voltage`: Battery voltage (for electric cars; for cars with internal combustion engine, this parameter is `null`).
* `cabin_temperature`: Temperature inside the car.
* `fuel_level`: Fuel level (for cars with internal combustion engine; for electric cars, this parameter is `null`).

This data will be transmitted as {{ RMQ }} messages. Each message will contain a JSON object as a string in the following format:

```json
{"device_id":"iv9a94th6rzt********","datetime":"2020-06-05 17:27:00","latitude":"55.70329032","longitude":"37.65472196","altitude":"427.5","speed":"0","battery_voltage":"23.5","cabin_temperature":"17","fuel_level":null}
```

The {{ mch-name }} cluster will use the [JSONEachRow format]({{ ch.docs }}/interfaces/formats/#jsoneachrow) for data inserted into the table, which converts strings from {{ RMQ }} messages to the appropriate column values.

Create a table in the {{ mch-name }} cluster to accept data from {{ RMQ }}:

1. [Connect](../../managed-clickhouse/operations/connect/clients.md#clickhouse-client) to the `db1` database in the {{ mch-name }} cluster using `clickhouse-client`.

1. Run this request:

    ```sql
    CREATE TABLE IF NOT EXISTS db1.cars (
        device_id String,
        datetime DateTime,
        latitude Float32,
        longitude Float32,
        altitude Float32,
        speed Float32,
        battery_voltage Nullable(Float32),
        cabin_temperature Float32,
        fuel_level Nullable(Float32)
    ) ENGINE = RabbitMQ
    SETTINGS
        rabbitmq_host_port = '<internal_IP_address_of_VM_with_RabbitMQ>:5672',
        rabbitmq_routing_key_list = 'cars',
        rabbitmq_exchange_name = 'exchange',
        rabbitmq_format = 'JSONEachRow';
    ```

This table will be automatically filled with messages read from the `cars` queue at {{ RMQ }}'s exchange point named `exchange`. When reading the data, {{ mch-name }} uses the [authentication credentials provided earlier](#configure-mch-for-rmq).

## Send the test data to the {{ RMQ }} queue {#send-sample-data-to-rmq}

1. Create a `sample.json` file with test data:

    ```json
    {
        "device_id": "iv9a94th6rzt********",
        "datetime": "2020-06-05 17:27:00",
        "latitude": 55.70329032,
        "longitude": 37.65472196,
        "altitude": 427.5,
        "speed": 0,
        "battery_voltage": 23.5,
        "cabin_temperature": 17,
        "fuel_level": null
    }

    {
        "device_id": "rhibbh3y08qm********",
        "datetime": "2020-06-06 09:49:54",
        "latitude": 55.71294467,
        "longitude": 37.66542005,
        "altitude": 429.13,
        "speed": 55.5,
        "battery_voltage": null,
        "cabin_temperature": 18,
        "fuel_level": 32
    }

    {
        "device_id": "iv9a94th6rzt********",
        "datetime": "2020-06-07 15:00:10",
        "latitude": 55.70985913,
        "longitude": 37.62141918,
        "altitude": 417.0,
        "speed": 15.7,
        "battery_voltage": 10.3,
        "cabin_temperature": 17,
        "fuel_level": null
    }
    ```

1. Send the data from the `sample.json` file to the previously created `cars` queue of the exchange point named `exchange` using `jq` and `amqp-publish`.

    ```bash
    jq \
    --raw-output \
    --compact-output . ./sample.json |\
    amqp-publish \
    --url=amqp://<RabbitMQ_username>:<password>@<IP_address_or_FQDN_of_the_RabbitMQ_server>:5672 \
    --routing-key=cars \
    --exchange=exchange
    ```

## Check that the test data is present in the {{ mch-name }} cluster table {#fetch-sample-data}

To access the data, use a materialized view (`MATERIALIZED VIEW`). When a materialized view is added to a `{{ RMQ }}` table, it starts collecting data in the background. This allows you to continuously receive messages from {{ RMQ }} and convert them to the required format using `SELECT`.

{% note info %}

Although you can read data directly from the table, we recommend using a materialized view, because every message from the queue can be read by {{ CH }} only once.

{% endnote %}

To create a materialized view for the `db1.cars` table:

1. [Connect](../../managed-clickhouse/operations/connect/clients.md#clickhouse-client) to the `db1` database in the {{ mch-name }} cluster using `clickhouse-client`.

1. Run the following requests:

    ```sql
    CREATE TABLE IF NOT EXISTS db1.cars_data_source (
        device_id String,
        datetime DateTime,
        latitude Float32,
        longitude Float32,
        altitude Float32,
        speed Float32,
        battery_voltage Nullable(Float32),
        cabin_temperature Float32,
        fuel_level Nullable(Float32)
    ) ENGINE MergeTree()
      ORDER BY device_id;

    CREATE MATERIALIZED VIEW db1.cars_view TO db1.cars_data_source
      AS SELECT * FROM db1.cars;
    ```

To get all the data from the `db1.cars_view` materialized view:

1. [Connect](../../managed-clickhouse/operations/connect/clients.md#clickhouse-client) to the `db1` database in the {{ mch-name }} cluster using `clickhouse-client`.

1. Run this request:

    ```sql
    SELECT * FROM db1.cars_view;
    ```

The query will return a table with data sent to {{ RMQ }}.

## Delete the resources you created {#clear-out}

Delete the resources you no longer need to avoid paying for them:

{% list tabs group=instructions %}

- Manually {#manual}

    * [Delete the {{ mch-full-name }} cluster](../../managed-clickhouse/operations/cluster-delete.md).
    * [Delete the virtual machine](../../compute/operations/vm-control/vm-delete.md).
    * If you reserved public static IP addresses, release and [delete them](../../vpc/operations/address-delete.md).

- {{ TF }} {#tf}

    {% include [terraform-clear-out](../../_includes/mdb/terraform/clear-out.md) %}

{% endlist %}
