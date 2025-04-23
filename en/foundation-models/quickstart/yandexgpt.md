# Getting started with {{ gpt-lite }} and {{ gpt-pro }}

In this section, you will learn how to use the {{ yagpt-name }} model to generate texts in [synchronous mode](../concepts/index.md#working-mode) without adding context. For other examples, see [Guides on how to use {{ foundation-models-full-name }}](../operations/index.md#yandexgpt-api)

The [management console]({{ link-console-main }}/link/foundation-models/yandexgpt) provides a quick and easy way for new users without a [billing account](../../billing/concepts/billing-account.md) to try out the model with {{ gpt-freetier }} free prompts per hour. To access the API and increase usage [quotas](../concepts/limits.md), [link](../../billing/operations/pin-cloud.md) your billing account to your cloud.

For information about {{ foundation-models-full-name }} pricing, see [{#T}](../pricing.md).

## Getting started {#before-begin}

To get started in {{ yandex-cloud }}:

1. Log in to the [management console]({{ link-console-main }}). If not signed up yet, navigate to the management console and follow the on-screen instructions.
1. In [{{ billing-name }}]({{ link-console-billing }}), make sure you have a [billing account](../../billing/concepts/billing-account.md) linked and its [status](../../billing/concepts/billing-account-statuses.md) is `ACTIVE` or `TRIAL_ACTIVE`. If you do not have a billing account yet, [create one](../../billing/quickstart/index.md#create_billing_account).
1. If you do not have a [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) yet, [create one](../../resource-manager/operations/folder/create.md).

{% list tabs group=instructions %}

- Management console {#console}

  You can start working from the management console right away.

- SDK {#sdk}

  To use the examples of requests using SDK:

  {% include [sdk-before-begin-ai-langmodel-user](../../_includes/foundation-models/sdk-before-begin-ai-langmodel-user.md) %}

- API {#api}

  To run sample requests using the API, install [cURL](https://curl.haxx.se). 

  To work with the {{ yagpt-name }} API, you need to get authenticated using your account:

  1. Get an IAM token: see the guide for a [Yandex account](../../iam/operations/iam-token/create.md) or [federated account](../../iam/operations/iam-token/create-for-federation.md).
  1. Get the [ID of the folder](../../resource-manager/operations/folder/get-id.md) for which your account has the `{{ roles-yagpt-user }}` role or higher.
  1. When accessing {{ gpt-lite }} or {{ gpt-pro }} via the API, provide the received parameters:

     * In the request file, specify the folder ID in the `modelUri` parameter.
     * In the request, specify the IAM token in the `Authorization` header.

     ```json
     Authorization: Bearer <IAM_token>
     ```

  For information about other API authentication methods, see [{#T}](../api-ref/authentication.md).

{% endlist %}

## Generate the text {#generate-text}

{% include [logging](../../_includes/foundation-models/yandexgpt/logging-disclaimer.md) %}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder for which your account has the `{{ roles-yagpt-user }}` role or higher.
  1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_foundation-models }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/dice-3.svg) **{{ ui-key.yacloud.yagpt.label_promt }}**.

     ![screen01](../../_assets/foundation-models/quickstart/yandexgpt/screen01.png)

  1. In the **{{ ui-key.yacloud.yagpt.label_temperature-text }}** field, enter a value between `0` and `1` for the model's response variability. With a higher value, you get a less deterministic result.
  1. Describe the request context under **{{ ui-key.yacloud.yagpt.label_instruction-text }}**.
  1. Describe your request to the model under **{{ ui-key.yacloud.yagpt.label_request-text }}**.
  1. Click **{{ ui-key.yacloud.yagpt.label_button-instruct-submit }}**. The answer will be shown on the right part of the screen.

     ![screen02](../../_assets/foundation-models/quickstart/yandexgpt/screen02.png)

- SDK {#sdk}

  {% include [yandexgpt-sdk-entire-generation-block](../../_includes/foundation-models/yandexgpt/yandexgpt-sdk-entire-generation-block.md) %}

- API {#api}

  1. Create a file with the request body, e.g., `prompt.json`:

     ```json
     {
       "modelUri": "gpt://<folder_ID>/yandexgpt",
       "completionOptions": {
         "stream": false,
         "temperature": 0.6,
         "maxTokens": "2000",
         "reasoningOptions": {
           "mode": "DISABLED"
         }
       },
       "messages": [
         {
           "role": "system",
           "text": "Find errors in the text and correct them."
         },
         {
           "role": "user",
           "text": "Laminate flooring is sutiable for instalation in the kitchen or in a child's room. It withsatnds moisturre and mechanical dammage thanks to a 0.2 mm thick proctive layer of melamine films and a wax-treated interlocking systme."
         }
       ]
     }
     ```

     Where:

     {% include [api-parameters](../../_includes/foundation-models/yandexgpt/api-parameters.md) %}

  1. Use the [completion](../text-generation/api-ref/TextGeneration/completion.md) method to send a request to the neural network in the following command:

     ```bash
     export FOLDER_ID=<folder_ID>
     export IAM_TOKEN=<IAM_token>
     curl \
       --request POST \
       --header "Content-Type: application/json" \
       --header "Authorization: Bearer ${IAM_TOKEN}" \
       --data "@prompt.json" \
       "https://llm.{{ api-host }}/foundationModels/v1/completion"
     ```

     Where:

     * `FOLDER_ID`: ID of the folder for which your account has the `{{ roles-yagpt-user }}` role or higher.
     * `IAM_TOKEN`: IAM token you got [before you started](#before-begin).

     The service will respond with the generated text:

     ```json
     {
       "result": {
         "alternatives": [
           {
             "message": {
               "role": "assistant",
               "text": "Laminate flooring is suitable for installation in the kitchen or in a child's room. It withstands moisture and mechanical damage thanks to a 0.2 mm thick protective layer of melamine films and a wax-treated interlocking system."
             },
             "status": "ALTERNATIVE_STATUS_TRUNCATED_FINAL"
           }
         ],
         "usage": {
           "inputTextTokens": "67",
           "completionTokens": "50",
           "totalTokens": "117"
         },
         "modelVersion": "06.12.2023"
       }
     }
     ```

{% endlist %}

#### What's next {#what-is-next}

* [Learn more about the service](../concepts/index.md)
* [Learn about API authentication methods](../api-ref/authentication.md)
* 