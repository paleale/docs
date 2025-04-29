# Setting up L7 load balancer logging

You can send [L7 load balancer](../concepts/application-load-balancer.md) [logs](../concepts/application-load-balancer.md#logging) to [{{ cloud-logging-full-name }}](../../logging/).

## Enabling logging {#enable-logs}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) with your load balancer.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_application-load-balancer }}**.
  1. Select the load balancer you need from the list, click ![image](../../_assets/console-icons/ellipsis.svg), and select **{{ ui-key.yacloud.common.edit }}**.
  1. Under **{{ ui-key.yacloud.alb.section_logs-settings }}**:
     1. Enable **{{ ui-key.yacloud.alb.label_log-requests }}**.
     1. Select the {{ cloud-logging-name }} [log group](../../logging/concepts/log-group.md) where you want to store load balancer logs.
     1. Click **{{ ui-key.yacloud.alb.button_add-discard-rule }}** and configure its [settings](../concepts/application-load-balancer.md#discard-logs-rules):
        * **{{ ui-key.yacloud.alb.label_discard-http-codes }}**: Add HTTP status codes.
        * **{{ ui-key.yacloud.alb.label_discard-http-code-intervals }}**: Add HTTP status code classes.
        * **{{ ui-key.yacloud.alb.label_discard-grpc-codes }}**: Add gRPC codes.
        * **{{ ui-key.yacloud.alb.label_discard-percent }}**: Set the log discard rate.

        You can add multiple rules.
  1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. See the description of the [CLI](../../cli/) command for managing load balancer logging:

     ```bash
     yc alb load-balancer logging --help
     ```

  1. Enable logging and configure {{ cloud-logging-name }} [logging](../logs-ref.md) settings:

     ```bash
     yc alb load-balancer logging <load_balancer_name> \
       --enable \
       --log-group-id <log_group_ID> \
       --discard codes=[<HTTP_code>,<HTTP_code_class>,<gRPC_code>],percent=<discarded_log_percentage>
     ```

     Where:
     * `--enable`: Enable logging.
     * `--log-group-id`: ID of the [log group](../../logging/concepts/log-group.md) that will store your load balancer logs.
     * `--discard`: [Log discard rule](../concepts/application-load-balancer.md#discard-logs-rules). Rule settings:
       * `codes`: HTTP codes, HTTP code classes, or gRPC codes.
       * `percent`: Log discard rate.

       You can add multiple rules.

     Result:

     ```text
     done (42s)
     id: ds76g8b2op3f*********
     name: test-load-balancer
     ...
     log_options:
       log_group_id: e23p9bfj2kyr********
       discard_rules:
         - http_codes:
             - "200"
           http_code_intervals:
             - HTTP_2XX
           grpc_codes:
             - OK
           discard_percent: "70"
     ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. Add the `log_options` section to the load balancer description in the configuration file:

     ```hcl
     log_options {
       log_group_id = "<log_group_ID>"
       discard_rule {
         http_codes          = ["200"]
         http_code_intervals = ["HTTP_2XX"]
         grpc_codes          = ["GRPC_OK"]
         discard_percent     = 75
       }
     }
     ```

     Where `log_options` are the {{ cloud-logging-name }} [logging](../logs-ref.md) options:
     * `log_group_id`: ID of the [log group](../../logging/concepts/log-group.md) that will store your load balancer logs.
     * `discard_rule`: [Log discard rule](../concepts/application-load-balancer.md#discard-logs-rules):
       * `http_codes`: HTTP codes.
       * `http_code_intervals`: HTTP code classes.
       * `grpc_codes`: gRPC codes.
       * `discard_percent`: Log discard rate.

       You can add multiple rules.
  1. Apply the changes:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

  This will enable logging for the specified load balancer. You can check the logging status and other load balancer settings in the [management console]({{ link-console-main }}) or using this CLI command:

  ```bash
  yc alb load-balancer get <load_balancer_name>
  ```

- API {#api}

  To enable logging, use the [update](../api-ref/LoadBalancer/update.md) REST API method for the [LoadBalancer](../api-ref/LoadBalancer/index.md) resource or the [LoadBalancerService/Update](../api-ref/grpc/LoadBalancer/update.md) gRPC API call.

{% endlist %}

## Updating logging settings {#update-logs}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder with your load balancer.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_application-load-balancer }}**.
  1. Select the load balancer you need from the list, click ![image](../../_assets/console-icons/ellipsis.svg), and select **{{ ui-key.yacloud.common.edit }}**.
  1. Under **{{ ui-key.yacloud.alb.section_logs-settings }}**:
     1. Change the {{ cloud-logging-name }} log group storing your load balancer logs.
     1. Edit [log discard rules](../concepts/application-load-balancer.md#discard-logs-rules):
        * **{{ ui-key.yacloud.alb.label_discard-http-codes }}**: Update HTTP status codes.
        * **{{ ui-key.yacloud.alb.label_discard-http-code-intervals }}**: Update HTTP status code classes.
        * **{{ ui-key.yacloud.alb.label_discard-grpc-codes }}**: Update gRPC codes.
        * **{{ ui-key.yacloud.alb.label_discard-percent }}**: Update the log discard rate.

        To add another rule, click **{{ ui-key.yacloud.alb.button_add-discard-rule }}**.
  1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for managing load balancer logging:

     ```bash
     yc alb load-balancer logging --help
     ```

  1. Update the {{ cloud-logging-name }} [logging](../logs-ref.md) settings:

     ```bash
     yc alb load-balancer logging <load_balancer_name> \
       --log-group-id <log_group_ID> \
       --discard codes=[<HTTP_code>,<HTTP_code_class>,<gRPC_code>],percent=<discarded_log_percentage>
     ```

     Where:
     * `--log-group-id`: ID of the [log group](../../logging/concepts/log-group.md) that will store your load balancer logs.
     * `--discard`: [Log discard rule](../concepts/application-load-balancer.md#discard-logs-rules). Rule settings:
       * `codes`: HTTP codes, HTTP code classes, or gRPC codes.
       * `percent`: Log discard rate.

       You can add multiple rules.

     Result:

     ```text
     done (42s)
     id: ds76g8b2op3f********
     name: test-load-balancer
     ...
     log_options:
       log_group_id: e23p9bfj2kyr********
       discard_rules:
         - http_codes:
             - "200"
           http_code_intervals:
             - HTTP_2XX
           grpc_codes:
             - OK
           discard_percent: "70"
     ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. In the configuration file with the load balancer description, update the logging settings in the `log_options` section:

     ```hcl
     log_options {
       log_group_id = "<log_group_ID>"
       discard_rule {
         http_codes          = ["200"]
         http_code_intervals = ["HTTP_2XX"]
         grpc_codes          = ["GRPC_OK"]
         discard_percent     = 75
       }
     }
     ```

     Where `log_options` are the {{ cloud-logging-name }} [logging](../logs-ref.md) options:
     * `log_group_id`: ID of the [log group](../../logging/concepts/log-group.md) that will store your load balancer logs.
     * `discard_rule`: [Log discard rule](../concepts/application-load-balancer.md#discard-logs-rules):
       * `http_codes`: HTTP codes.
       * `http_code_intervals`: HTTP code classes.
       * `grpc_codes`: gRPC codes.
       * `discard_percent`: Log discard rate.

       You can add multiple rules.
  1. Apply the changes:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

  This will update logging settings for the specified load balancer. You can check the load balancer settings in the [management console]({{ link-console-main }}) or using this [CLI](../../cli/quickstart.md) command:

  ```bash
  yc alb load-balancer get <load_balancer_name>
  ```

- API {#api}

  To update logging settings, use the [update](../api-ref/LoadBalancer/update.md) REST API method for the [LoadBalancer](../api-ref/LoadBalancer/index.md) resource or the [LoadBalancerService/Update](../api-ref/grpc/LoadBalancer/update.md) gRPC API call.

{% endlist %}

## Disabling logging {#disable-logging}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder with your load balancer.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_application-load-balancer }}**.
  1. Select the load balancer you need from the list, click ![image](../../_assets/console-icons/ellipsis.svg), and select **{{ ui-key.yacloud.common.edit }}**.
  1. Under **{{ ui-key.yacloud.alb.section_logs-settings }}**, disable **{{ ui-key.yacloud.alb.label_log-requests }}**.
  1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for managing load balancer logging:

     ```bash
     yc alb load-balancer logging --help
     ```

  1. Disable logging:

     ```bash
     yc alb load-balancer logging <load_balancer_name> --disable
     ```

     Where `--disable` is the logging disable option.

     Result:

     ```text
     done (42s)
     id: ds76g8b2op3f********
     name: test-load-balancer
     ...
     log_options:
       disable: true
     ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. Delete the `log_options` section from the configuration file with the load balancer description:

     ```hcl
     log_options {
     ...
     }
     ```

  1. Apply the changes:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     This will disable logging for the specified load balancer. You can check the logging status and other load balancer settings in the [management console]({{ link-console-main }}) or using this [CLI](../../cli/quickstart.md) command:

     ```bash
     yc alb load-balancer get <load_balancer_name>
     ```

- API {#api}

  To disable logging, use the [update](../api-ref/LoadBalancer/update.md) REST API method for the [LoadBalancer](../api-ref/LoadBalancer/index.md) resource or the [LoadBalancerService/Update](../api-ref/grpc/LoadBalancer/update.md) gRPC API call.

{% endlist %}