# Setting up logging

{% list tabs group=instructions %}

- Management console {#console}

    1. In the [management console]({{ link-console-main }}), select the folder containing the notification channel.
    1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_cns }}**.
    1. Select a channel.
    1. At the top right, click ![image](../../_assets/edit.svg) **{{ ui-key.yacloud.common.edit }}**.
    1. Under **{{ ui-key.yacloud.cns.section_logging }}**, enable the **{{ ui-key.yacloud.cns.field_logging }}** option.
    1. In the **{{ ui-key.yacloud.cns.field_logging-folder }}** list, select a folder to house the [log group](../../logging/concepts/log-group.md).
    1. In the **{{ ui-key.yacloud.cns.field_log-group }}** field, select an existing log group or create a new one.
    1. To stop logging, disable **{{ ui-key.yacloud.cns.field_logging }}**.

- AWS CLI {#aws-cli}

    1. If you do not have the AWS CLI yet, [install and configure it](../../storage/tools/aws-cli.md).
    1. Run this command:

        ```bash
        aws sns set-endpoint-attributes \
            --endpoint-arn <endpoint_ARN> \
            --attributes LoggingPath=<folder_ID>/<log_group_ID>
        ```

        Where:

        * `--endpoint-arn`: Endpoint ID (ARN).
        * `--attributes`: Endpoint parameters. To configure logging, use the `LoggingPath` parameter.
        * `folder_ID`: ID of the folder housing the [log group](../../logging/concepts/log-group.md). The folder must be in the same cloud as the app.
        * `log_group_ID`: ID of the log group to send logs to. This is an optional parameter; if no ID is specified, the default log group is used.

    1. To disable logging, provide an empty value for the `LoggingPath` attribute.        

    For more information about the `aws sns set-endpoint-attributes` command, see the [AWS documentation](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sns/set-endpoint-attributes.html).


{% endlist %}