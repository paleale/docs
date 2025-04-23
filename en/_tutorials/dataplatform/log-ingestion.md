# Smart log processing

Apps generate logs to enable diagnostics. However, it is not enough just to have logs for analysis: you need to be able to store and process them in a convenient way. This is why logs go to storage systems: [Hadoop](/services/data-proc), [{{ CH }}](/services/managed-clickhouse), or specialized cloud systems such as [{{ cloud-logging-name }}](../../logging/).

Applications do not usually write logs to storage systems directly. Instead, they send them to intermediate aggregator applications. These aggregators can receive logs by intercepting stdout/stderr streams, read log files from disk, get them via syslog or over HTTP, and in many other ways.

![log-aggregator](../../_assets/data-streams/log-aggregator.svg)

After receiving logs, aggregator applications accumulate them and then send them to different targets using plugins. This approach lets application developers focus on writing code and delegate log delivery to specially allocated systems.

Standard log delivery systems are [fluentd](https://www.fluentd.org), [fluentbit](https://fluentbit.io), [logstash](https://www.elastic.co/logstash/), and more.

Though aggregator applications can write data to storage systems directly, to enhance reliability, data is first sent to an intermediate buffer (a data streaming bus, message broker), that is {{ yds-full-name }}, and then to a storage system from it.

Logs often contain too much data or restricted information. You can mask unnecessary or confidential information by adding more processing steps, e.g., in {{ sf-name }}.


## Benefits {#advantages}

### Reliability {#reliability}

To increase reliability, applications only need to configure a log aggregator to deliver data to a bus as quickly as possible, and the bus will ensure reliable data storage up to the point when the data is processed and written to storage systems.


### Multiple storage systems {#multiple}

The same logs are often stored in multiple storage systems at once: in {{ CH }} for fast analysis and in {{ objstorage-name }} for long-term storage. To implement this, you can set up your aggregator applications so that they send two data streams: one to {{ CH }} and the other one to {{ objstorage-name }}.

With data buses, this can be done even more easily: just send a log once to a data bus and then, from it, run two data transfer processes inside {{ yandex-cloud }}. This solution will also let you add a third storage system, such as {{ GP }} or {{ ES }}, at any time.

The multiple storage system approach is very convenient for ensuring compliance with FZ-152, PCI DSS, and other standards requiring that logs be stored for at least one year. In which case, while the last month's logs go to a quick access storage system, the rest of the data may be sent to a long-term "cold" storage in {{ objstorage-name }}.


### Masking data and processing logs {#mask}

Some logs are not accessible to all employees. For example, logs may contain users' personal data access to which must be limited.

Transmitted logs can be sent to {{ sf-name }} where they can by masked or handled in any other way.

Once processed, the logs can be sent to multiple target systems at once: access to the logs containing masked personal data can be granted to all employees while access to the full logs to administrators only.


## Configuration {#setup}

To configure smart log processing:

1. [Create a data stream](../../data-streams/quickstart/create-stream.md) {{ yds-short-name }}.
1. Set up a log aggregator: [fluentd](../../data-streams/quickstart/fluentd.md), [logstash](../../data-streams/quickstart/logstash.md), or any other aggregator that supports the [Kinesis Data Streams API](../../data-streams/kinesisapi/api-ref.md).
1. Set up {{ data-transfer-full-name }} to transfer data to the selected storage system.

    An example of setting up data delivery from {{ yds-short-name }} is given in the [tutorial on how to save data to {{ CH }}](../../data-streams/tutorials/send-to-clickhouse.md).

1. Connect an arbitrary data processing function to {{ data-transfer-full-name }}. This Github [example](https://github.com/yandex-cloud-examples/yc-nginx-logs-data-transfer) shows the function code.

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}