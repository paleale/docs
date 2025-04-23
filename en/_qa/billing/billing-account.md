# Questions about the billing account

#### How do I get the billing account ID? {#account-id}

To get a billing account's ID:

{% include [billing-account-id](../../_includes/billing/billing-account-id.md) %}

#### How many billing accounts can I create? {#account-amount}

{% include [billing-account-amounts](../../billing/_includes/billing-account-amounts.md) %}

{% include [billing-account-payers](../../billing/_includes/billing-account-payers.md) %}

{% include [billing-account-payers-example](../../billing/_includes/billing-account-payers-example.md) %}

#### I have created a personal billing account. Can I change it into a business account? {#account-type}

You cannot change your billing account [type](../../billing/concepts/billing-account.md#ba-types) once it is created, but you can [create a new account](../../billing/operations/create-new-account.md) of the type you need.

#### I have created a new business billing account, but it is blocked. Why? {#blocked-account}

Most likely, you had previously created a business billing account for which a trial period was activated. Your billing account gets the initial grant as part of the trial period. As the initial grant can only be issued to one billing account, you cannot use the trial period after you register a new account. As a result, your new billing account gets `SUSPENDED`.
To unblock your new billing account, [upgrade to the paid version](../../billing/operations/activate-commercial.md) and [top up your personal account](../../billing/operations/pay-the-bill.md) to the minimum amount. You can check the minimum amount in the **{{ ui-key.yacloud_billing.billing.account.dashboard.button_refill }}** window.

#### I received no email with instructions on what to do after creating a billing account with the {{ ui-key.yacloud_billing_account.cloud-billing-account.payment-type_label_invoice }} payment method. Why? {#account-notification}

During the activation of your billing account, your documents and data get checked. With that done, your billing account can be activated and you can start using {{ yandex-cloud }}.
If you still get no email, inquire at [{{ billing-docs-email }}](mailto:{{ billing-docs-email }}).

{% include [change-payment-docs](../../billing/_includes/change-payment-docs.md) %}

#### Why do I get an error saying _The country is not available to the payer_ when creating a billing account? {#account-error}

Most likely you have logged in under a Yandex account already linked to a payer with a different residence status. 

{% include [billing-account-payers](../../billing/_includes/billing-account-payers.md) %}

To create a billing account with a payer being a resident of a different country, log in using a different Yandex ID or under a different Yandex 360 account. 

{% include [billing-account-payers-example](../../billing/_includes/billing-account-payers-example.md) %}

#### I cannot link a bank card when creating a billing account. What should I do? {#cant-create}

Most likely, our service does not support your bank card's payment system.

To resolve the issue, try linking a different bank card or, if you are a legal entity, create a billing account of the **{{ ui-key.yacloud_billing.billing.account.create-new.account-type_label_company }}** type with the **{{ ui-key.yacloud_billing.billing.account.create-new.payment-type_label_invoice }}** payment method.

#### What happens if I do not link a bank card when creating a billing account? {#no-linked-card}

{% include [initail-payment-amout](../../_includes/billing/initial-payment-amount.md) %}

{% note warning %}

If you have not linked a bank card when creating your account, you will not be eligible for the [initial grant](../../billing/concepts/bonus-account.md), even if you link a card later.

{% endnote %}

#### How many clouds can I link to my billing account? {#clouds-amount}

{% include [pin-cloud-note](../../billing/_includes/pin-cloud-note.md) %}

#### How do I change the billing account linked to a cloud? {#cloud-transfer}

To change the billing account of a cloud, you simply need to [link](../../billing/operations/pin-cloud.md) it to a different account. This automatically unlinks the cloud from its current account. To avoid running into arrears, top up your [personal account](../../billing/concepts/personal-account.md) in the billing account the cloud was linked to.
``

#### Why cannot I link a cloud to my billing account? {#cannot-pin-cloud}

If your billing account is added to an organization, you can only link to it the resources that are in the same organization. You may not have [permissions to work](../../billing/operations/pin-cloud.md#bind-roles) with this cloud or billing account. Ask your [organization](../../billing/concepts/organization.md) administrator to [grant you the roles you need](../../billing/security/index.md#set-role).
Roles can be issued either directly to the billing account and cloud, or to the organization they reside in.

#### How do I enable a billing threshold? {#credit}

The [billing threshold](../../billing/concepts/billing-threshold.md) gets enabled automatically after you [upgrade to the paid version](../../billing/operations/activate-commercial.md) and the first reporting period ends.

{% include [change-payment-method](../../billing/_includes/change-payment-method.md) %}

#### How can I change my payment details or the delivery address? {#change-email}

{% include [change-address](../../billing/_includes/change-address.md) %}


#### How do I change the billing account owner? {#account-owner}

You cannot change the billing account owner. If you still need this, a new owner may [create a billing account](../../billing/operations/create-new-account.md) and [link](../../billing/operations/pin-cloud.md) clouds with all their resources to this billing account.

#### Where can add a bank account? {#checking-account}

To specify a bank account for your billing account:

{% list tabs group=instructions %}

- {{ billing-interface }} {#billing}

   1. {% include [move-to-billing-step](../../billing/_includes/move-to-billing-step.md) %}
   1. Select a billing account.
   1. Go to the **{{ ui-key.yacloud_billing.billing.account.switch_overview }}** page.
   1. Click **{{ ui-key.yacloud_billing.billing.account.dashboard-info.company_label_edit_ru }}**.
      This will redirect you to [Yandex Balance](https://balance.yandex.ru/).
   1. Select a payer and click **{{ ui-key.yacloud.common.edit }}**.
   1. Under **Payment details**, fill out the **{{ ui-key.yacloud_billing.billing.account.overview.company_label_rs }}** field.

{% endlist %}

#### How do I find out if my account is managed via a {{ yandex-cloud }} organization? {#check-ba-org}

{% include [account-in-organization](../../_includes/billing/check-account-organization.md) %}

#### Can I pay for the service resources of another {{ yandex-cloud }} organization? {#pay-org}

Yes, you can.

{% include [account-in-organization](../../_includes/billing/pay-resouces-of-another-organization.md) %}

See [Managing a billing account in an organization](../../billing/concepts/organization.md) for details.

#### How do I stop using the platform and delete my billing account? {#delete-account}


Delete all [clouds](../../resource-manager/concepts/resources-hierarchy.md#cloud), [folders](../../resource-manager/concepts/resources-hierarchy.md#folder), and resources within them, pay the outstanding balance, and select **{{ ui-key.yacloud_billing.billing.account.dashboard.label_inactivation }}** on the billing account page in [**{{ billing-name }}**]({{ link-console-billing }}). For more information, see [{#T}](../../billing/operations/delete-account.md).


