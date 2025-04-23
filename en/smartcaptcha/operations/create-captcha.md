---
title: How to create a CAPTCHA in {{ captcha-full-name }}
description: Follow this guide to create a CAPTCHA.
---

# Creating a CAPTCHA

{{ captcha-name }} allows you to create [CAPTCHAs](../concepts/validation.md) of multiple difficulty levels for various incoming requests. The difficulty level is determined by the basic properties of the incoming traffic.

When creating a CAPTCHA, you can:

* Connect a CAPTCHA to multiple websites.
* Set up the CAPTCHA appearance, such as background, states, errors, and the **I'm not a robot** button style.
* Select the type and difficulty level of a CAPTCHA challenge.
* Show various CAPTCHA options based on the incoming request properties, such as use different CAPTCHA for the users from different countries.

{% include [user-data-to-ml](../../_includes/smartcaptcha/user-data-to-ml.md) %}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_smartcaptcha }}**.
  1. Click **{{ ui-key.yacloud.smartcaptcha.button_captcha-settings-create }}**.

     ![screen01](../../_assets/smartcaptcha/create-captcha/screen01.png)

  1. Specify the **{{ ui-key.yacloud.common.name }}** of the CAPTCHA you are creating:

      {% include [name-format](../../_includes/smartcaptcha/name-format.md) %}

  1. Optionally, select [**{{ ui-key.yacloud.smartcaptcha.label_no-hostname-check }}**](../concepts/domain-validation.md).
  1. Specify **{{ ui-key.yacloud.smartcaptcha.label_allowed-sites }}** as IP addresses or domain names without `http`/`https` or`/` at the end, e.g., `example.com`.

     The CAPTCHA will also be valid for all subdomains of the specified domain names.

  1. Set up the **{{ ui-key.yacloud.smartcaptcha.label_section-style }}** of the **I'm not a robot** button and the challenge window:
     * `{{ ui-key.yacloud.smartcaptcha.value_config-standard }}`: Default appearance
     * `Gray`
     * `Dark theme`
     * `Blue`

      Under **{{ ui-key.yacloud.smartcaptcha.label_section-customization }}**, you can set custom properties for the challenge window and other elements through the form or style description in `JSON` format.

      All changes will be displayed in the preview window.

     ![screen02](../../_assets/smartcaptcha/create-captcha/screen02.png)

  1. Configure the **Challenge options**. You can only specify the default CAPTCHA or add other [options](../concepts/captcha-variants.md) if you want to show different CAPTCHAs for different requests.
  
  1. For the **Default** option, specify:
      * **{{ ui-key.yacloud.smartcaptcha.label_main-challenge }}**: Type of the [main challenge](../concepts/tasks.md#main-task) the user will get.
      * **{{ ui-key.yacloud.smartcaptcha.label_additional-challenge }}**: Type of the [additional challenge](../concepts/tasks.md#additional-task) the user will get.
      * **{{ ui-key.yacloud.smartcaptcha.label_complexity }}**: [Difficulty level](../concepts/tasks.md#task-difficulty) of the challenge the user will get:
      * `{{ ui-key.yacloud.smartcaptcha.value_complexity-easy }}`: Simple challenge.
      * `{{ ui-key.yacloud.smartcaptcha.value_complexity-medium }}`: Medium level challenge.
      * `{{ ui-key.yacloud.smartcaptcha.value_complexity-hard }}`: Difficult challenge.
      * `{{ ui-key.yacloud.smartcaptcha.value_complexity-force_hard }}`: Difficult challenge with an additional question. The user is required to solve an additional challenge, regardless of the results of the main one.

        You can see a sample challenge in the preview window.

        {% include [note-preview-captcha-variants](../../_includes/smartcaptcha/note-preview-captcha-variants.md) %}

  1. To show different CAPTCHAs for different requests:
     1. Click **Add option** and configure the settings as you would do for default CAPTCHA.

        To delete an option, click ![options](../../_assets/console-icons/trash-bin.svg). If an option is used in the show rule, you cannot delete it.

     1. Add the [rules for incoming traffic](../concepts/captcha-variants.md#captcha-view-rules) that will determine which CAPTCHA option to show:
         * Click **Add rule**.
         * Specify the rule name and description.
         * Select a CAPTCHA option.
         * Specify the rule priority from `1` to `999999`.
          
            Rules are checked in the ascending priority order, starting from `1`, `2`, etc. If the traffic matches multiple rules, the first rule to trigger will apply.
          
         * Specify one or more [conditions for the incoming traffic](../concepts/captcha-variants.md#traffic-conditions):
            * `IP`: IP address, IP address range, or IP address region.
            * `HTTP header`: HTTP header string.
            * `URI`: Path of the incoming request to the website.
            * `Host`: Domain receiving the request.
           
         * Click **Add**.
          
            To delete a rule, click ![options](../../_assets/console-icons/trash-bin.svg). You cannot delete a default rule.

     1. Add other CAPTCHA options and rules for incoming traffic in a similar way.

  1. Optionally, enable or disable the use of HTTP request information to improve machine learning models under **{{ ui-key.yacloud.component.disallow-data-processing.title_ml-model-training }}**.
   
  1. Click **{{ ui-key.yacloud.common.create }}**.

     ![screen03](../../_assets/smartcaptcha/create-captcha/screen03.png)

  The CAPTCHA will appear on the service page under **{{ ui-key.yacloud.smartcaptcha.label_captcha-settings-list }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. View the description of the CLI command for creating a CAPTCHA:

     ```bash
     yc smartcaptcha captcha create --help
     ```

  1. Create a CAPTCHA:

     ```bash
     yc smartcaptcha captcha create \
       --name <captcha_name>
       --turn-off-hostname-check \
       --allowed-site <first_host>,<second_host> \
       --style-json '<captcha_appearance>' \
       --pre-check-type <main_challenge_type> \
       --challenge-type <additional_challenge_type> \
       --complexity <challenge_difficulty> \
       --override-variants-file <path_to_file_with_rules>.yaml \
       --security-rules-file <path_to_file_with_options>.yaml
     ```
 
     Where:
     * `--name`: CAPTCHA name.
     * `--turn-off-hostname-check`: [Disable domain check](../concepts/domain-validation.md). This is an optional parameter.
     * `--allowed-site`: List of hosts as IP addresses or domain names. without `http`/`https` or `/` at the end, e.g., `example.com`. The CAPTCHA will also be valid for all subdomains of the specified domain names. This is an optional parameter.
     * `--style-json`: Appearance of the challenge window and other elements, in `JSON` format. For more details, see the `JSON` generated using the [management console]({{ link-console-main }}). This is an optional parameter.
     * `--pre-check-type`: Type of the default [main challenge](../concepts/tasks.md#main-task) the user will get.
     * `--challenge-type`: Type of the default [additional challenge](../concepts/tasks.md#additional-task) the user will get.
     * `--complexity`: Default challenge [difficulty](../concepts/tasks.md#task-difficulty). The possible values are:
       * `EASY`: Simple challenge.
       * `MEDIUM`: Medium level challenge.
       * `HARD`: Difficult challenge.
       * `FORCE_HARD`: Difficult challenge with an additional question. The user is required to solve an additional challenge, regardless of the results of the main one.
 
       {% include [note-preview-captcha-variants](../../_includes/smartcaptcha/note-preview-captcha-variants.md) %}
 
     * `--security-rules-file`: Path to a `YAML` file containing the [rules for incoming traffic](../concepts/captcha-variants.md#captcha-view-rules) that will determine which CAPTCHA option to show. This is an optional parameter.
 
       {% cut "Example of a file with rules for incoming traffic" %}
 
       ```yaml
       - name: <rule_1_name>
         priority: "<rule_1_priority>"
         description: <rule_1_description>
         override_variant_uuid: <challenge_variant_ID>
         condition:
           host:
             hosts:
               - exact_match: example.com
               - exact_match: example.net
       - name: <rule_2_name>
         priority: "<rule_2_priority>"
         description: <rule_2_description>
         override_variant_uuid: <challenge_variant_ID>
         condition:
           source_ip:
             geo_ip_match:
               locations:
                 - ru
                 - es
       ```
 
       Where:
       * `name`: Rule name.
       * `priority`: Rule priority, from `1` to `999999`.
         
         Rules are checked in the ascending priority order, starting from `1`, `2`, etc. If traffic matches multiple rules, the first rule to trigger will apply.
 
       * `description`: Rule description. This is an optional parameter.
       * `override_variant_uuid`: ID of the challenge option to show if the traffic matches the rule. If the parameter is not specified, the user will get the default challenge.
       * `condition`: One or more [conditions for the incoming traffic](../concepts/captcha-variants.md#traffic-conditions). This is an optional parameter.
 
       {% endcut %}
 
     * `--override-variants-file`: Path to a `YAML` file with challenge [options](../concepts/captcha-variants.md). This is an optional parameter.
 
       {% cut "Example of a file with challenge options" %}
 
       ```yaml
       - uuid: <option_1_ID>
         description: <option_1_description>
         complexity: <challenge_difficulty>
         pre_check_type: <main_challenge>
         challenge_type: <additional_challenge>
       - uuid: <option_2_ID>
         description: <option_2_description>
         complexity: <challenge_difficulty>
         pre_check_type: <main_challenge>
         challenge_type: <additional_challenge>
       ```
 
       Where:
       * `uuid`: Unique ID of the challenge option.
       * `description`: Challenge option description. 
       * `complexity`: Difficulty level of the challenge the user will get.
       * `pre_check_type`: Type of the main challenge the user will get.
       * `challenge_type`: Type of the additional challenge the user will get.
 
       {% endcut %}

  {% include [cli-creation-result](../../_includes/smartcaptcha/cli-creation-result.md) %}

- Terraform {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  To create a CAPTCHA:

  1. In the {{ TF }} configuration file, define the parameters of the resources you want to create:

     {% include [tf-creation-configuration](../../_includes/smartcaptcha/tf-creation-configuration.md) %}

     Where:
     * `name`: CAPTCHA name.
     * `style_json`: Appearance of the challenge window and other elements, in `JSON` format. For more details, see the `JSON` generated using the [management console]({{ link-console-main }}). This is an optional parameter.
     * `complexity`: [Difficulty](../concepts/tasks.md#task-difficulty) level of the default challenge the user will get. The possible values are:
       * `EASY`: Simple challenge.
       * `MEDIUM`: Medium level challenge.
       * `HARD`: Difficult challenge.
       * `FORCE_HARD`: Difficult challenge with an additional question. The user is required to solve an additional challenge, regardless of the results of the main one.

       {% include [note-preview-captcha-variants](../../_includes/smartcaptcha/note-preview-captcha-variants.md) %}

     * `pre_check_type`: Type of the default [main challenge](../concepts/tasks.md#main-task) the user will get.
     * `challenge_type`: Type of the default [additional challenge](../concepts/tasks.md#additional-task) the user will get.
     * `allowed_sites`: List of hosts as IP addresses or domain names without `http`/`https` or `/` at the end, e.g., `example.com`. The CAPTCHA will also be valid for all subdomains of the specified domain names. This is an optional parameter.
     * `override_variant`: Section containing the challenge [option](../concepts/captcha-variants.md) description. This is an optional parameter.
       * `uuid`: Unique ID of the challenge option.
       * `description`: Challenge option description. This is an optional parameter.
       * `complexity`: Difficulty level of the challenge the user will get.
       * `pre_check_type`: Type of the main challenge the user will get.
       * `challenge_type`: Type of the additional challenge the user will get.
     * `security_rule`: Section describing the [rule for incoming traffic](../concepts/captcha-variants.md#captcha-view-rules) that will determine which CAPTCHA option to show. This is an optional parameter.
       * `name`: Rule name.
       * `priority`: Rule priority, from `1` to `999999`.

         Rules are checked in the ascending priority order, starting from `1`, `2`, etc. If traffic matches multiple rules, the first rule to trigger will apply.

       * `description`: Rule description. This is an optional parameter.
       * `override_variant_uuid`: ID of the challenge option to show if the traffic matches the rule. If the parameter is not specified, the user will get the default challenge.
       * `condition`: One or more [conditions for the incoming traffic](../concepts/captcha-variants.md#traffic-conditions). This is an optional parameter.

     For more information about the `yandex_smartcaptcha_captcha` resource parameters, see the [provider documentation]({{ tf-provider-resources-link }}/smartcaptcha_captcha).

  1. Create the resources:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     {{ TF }} will create all the required resources. You can check the new resources using the [management console]({{ link-console-main }}) or this [CLI](../../cli/) command:

     ```bash
     yc smartcaptcha captcha list
     ```

- API {#api}

  To create a CAPTCHA, use the [create](../../smartcaptcha/api-ref/Captcha/create.md) REST API method for the [Captcha](../../smartcaptcha/api-ref/Captcha/index.md) resource or the [Captcha/Create](../../smartcaptcha/api-ref/grpc/Captcha/create.md) gRPC API call.

{% endlist %}
