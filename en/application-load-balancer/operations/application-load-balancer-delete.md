---
title: How to delete an L7 load balancer in {{ alb-full-name }}
description: In this tutorial, you will learn how to delete an L7 load balancer.
---

# Deleting an L7 load balancer

To delete an L7 load balancer:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder with your load balancer.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_application-load-balancer }}**.
  1. Click ![image](../../_assets/console-icons/ellipsis.svg) next to the load balancer you want to delete, then select **{{ ui-key.yacloud.common.delete }}**.

     To delete many load balancers at once, select them in the list and click **{{ ui-key.yacloud.common.delete }}** at the bottom of the screen.
  1. In the window that opens, click **{{ ui-key.yacloud.common.delete }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for deleting a load balancer:

     ```bash
     yc alb load-balancer delete --help
     ```

  1. Run this command:

     ```bash
     yc alb load-balancer delete <load_balancer_name_or_ID>
     ```

     Result:

     ```text
     done (1m10s)
     ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  To delete an L7 load balancer created with {{ TF }}:
  1. Open the {{ TF }} configuration file and delete the part describing the L7 load balancer.

     {% cut "Sample L7 load balancer description in the {{ TF }} configuration" %}

     ```hcl
     ...
     resource "yandex_alb_load_balancer" "test-balancer" {
       name        = "<load_balancer_name>"
       network_id  = yandex_vpc_network.test-network.id

       allocation_policy {
         location {
           zone_id   = "{{ region-id }}-a"
           subnet_id = yandex_vpc_subnet.test-subnet.id
         }
       }

       listener {
         name = "<listener_name>"
         endpoint {
           address {
             external_ipv4_address {
             }
           }
           ports = [ 9000 ]
         }
         http {
           handler {
             http_router_id = yandex_alb_http_router.test-router.id
           }
         }
       }
     }
     ...
     ```

     {% endcut %}

  1. In the command line, navigate to the directory with the {{ TF }} configuration file.
  1. Check the configuration using this command:

     ```bash
     terraform validate
     ```

     If the configuration is correct, you will get this message:

     ```bash
     Success! The configuration is valid.
     ```

  1. Run this command:

     ```bash
     terraform plan
     ```

     You will see a detailed list of resources. No changes will be made at this step. If your configuration contains errors, {{ TF }} will show them.
  1. Apply the changes:

     ```bash
     terraform apply
     ```

  1. Type `yes` and press **Enter** to confirm changes.

     You can check updates in the [management console]({{ link-console-main }}) or using this [CLI](../../cli/quickstart.md) command:

     ```bash
     yc alb load-balancer list
     ```

- API {#api}

  Use the [delete](../api-ref/LoadBalancer/delete.md) REST API method for the [LoadBalancer](../api-ref/LoadBalancer/index.md) resource or the [LoadBalancerService/Delete](../api-ref/grpc/LoadBalancer/delete.md) gRPC API call.

{% endlist %}