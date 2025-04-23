# User group mapping in {{ microsoft-idp.entra-id-full }}

You can use [{{ microsoft-idp.entra-id-full }}](https://www.microsoft.com/ru-ru/security/business/identity-access/microsoft-entra-id) (formerly {{ microsoft-idp.azure-ad-legacy }}) to authenticate users in an organization.

To configure user group mapping in {{ microsoft-idp.entra-id-short }} and in an [identity federation](../../organization/concepts/add-federation.md):

1. [Start configuring an application in Azure](#azure-settings-begin).
1. [Create a federation in {{ org-full-name }}](#create-federation).
1. [Add the application's SAML certificate to the federation](#add-certificate).
1. [Complete configuring the application](#azure-settings-end).
1. [Configure group mapping on the application side](#azure-mapping).
1. [Configure group mapping on the federation side](#org-mapping).
1. [Test authentication](#test-auth).

## Getting started {#before-you-begin}

Make sure you have access to the following services on the [Azure portal](https://portal.azure.com/):

* Enterprise applications.
* {{ microsoft-idp.entra-id-full }}.

## Start configuring an application in Azure {#azure-settings-begin}

The identity provider's (IdP) role is played by Microsoft Azure with Single Sign-On (SSO) configured. To create an application and begin configuring it:

1. [Go to the Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **Enterprise applications**.
1. On the left-hand panel, select **Enterprise applications** → **All applications**.
1. Click **New application**.
1. On the **Browse {{ microsoft-idp.entra-full }}** gallery page, click **Create your own application**.
1. In the window that opens:
    1. Name your app, e.g., `yandex-cloud-saml`.
    1. Select **Integrate any other application you don't find in the gallery**.
    1. Click **Create**.

    You will be taken to your new app's page.

1. In the left-hand panel, select **Single sign-on**.
1. Select the **SAML** single sign-on.

    The **SAML-based sign-on** page will open.

1. Download the application's SAML certificate used to sign messages from {{ microsoft-idp.entra-id-short }}:

    1. Find **SAML certificates** → **Assertion signing certificate**.
    1. Use the link in the **Certificate (Base64)** field to download the certificate.

1. Save the credentials you will need later to configure your identity federation:

    1. Find the **yandex-cloud-saml configuration** section.

        If you have chosen a different application name, the section name will be different from the one provided.

    1. Save the following credentials:

        * **Login page URL** in the following format:

            ```text
            https://login.microsoftonline.com/<tenant_ID>/saml2
            ```

        * **{{ microsoft-idp.entra-full }}** ID in the following format:

            ```text
            https://sts.windows.net/<tenant_ID>/
            ```

{% note info %}

The configuring of SAML-based sign-on for the application will continue after you create an identity federation.

Do not close the configuration tab in your browser.

{% endnote %}

## Create a {{ org-full-name }} federation {#create-federation}

{% list tabs group=instructions %}

- {{ cloud-center }} interface {#cloud-center}

  1. Go to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. In the left-hand panel, select ![icon-federation](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Click ![Circles3Plus](../../_assets/console-icons/circles-3-plus.svg) **{{ ui-key.yacloud_org.form.federation.action.create }}** in the top-right corner of the page. In the window that opens:

      1. Enter a name for the federation, e.g., `demo-federation`. It must be unique within the folder.

      1. You can also add a description, if required.

      1. In the **{{ ui-key.yacloud_org.entity.federation.field.cookieMaxAge }}** field, specify the time before the browser asks the user to re-authenticate.

      1. In the **{{ ui-key.yacloud_org.entity.federation.field.issuer }}** field, paste the {{ microsoft-idp.entra-full }} ID [you got when configuring the Azure app](#azure-settings-begin).

      1. In the **{{ ui-key.yacloud_org.entity.federation.field.ssoUrl }}** field, paste the login page URL you got when configuring the Azure app.

      1. Enable **{{ ui-key.yacloud_org.entity.federation.field.autocreateUsers }}** to automatically add a new user to your organization after authentication. Otherwise, you will need to [manually add](../../organization/operations/add-account.md#add-user-sso) your federated users.

          {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

      1. (Optional) To make sure that all authentication requests from {{ yandex-cloud }} contain a digital signature, enable **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}**. You will need to install a {{ yandex-cloud }} SAML certificate on the IdP side.

          {% include [download-saml-cert-when-creating-fed](../../_includes/organization/download-saml-cert-when-creating-fed.md) %}

          {% include [setup-cert-for-idp](../../_includes/organization/setup-cert-for-idp.md) %}

          You will need this certificate later when configuring SAML-based sign-on for the Azure app.

      1. {% include [forceauthn-option-enable](../../_includes/organization/forceauthn-option-enable.md) %}

      1. Click **{{ ui-key.yacloud_org.form.federation.create.action.create }}**.

{% endlist %}

## Add the Azure app's SAML certificate to the federation {#add-certificate}

To enable {{ org-name }} to verify the app's SAML certificate during authentication, add the certificate to the federation:

{% list tabs group=instructions %}

- {{ cloud-center }} interface {#cloud-center}

  1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Click the row with `demo-federation` to add your certificate to.

  1. Click **{{ ui-key.yacloud_org.page.federation.section.certificates }}** under **{{ ui-key.yacloud_org.entity.certificate.action.add }}** at the bottom of the page.

  1. Enter certificate name and description.

  1. In the **{{ ui-key.yacloud_org.component.form-file-upload.field.method }}** field, select `{{ ui-key.yacloud_org.component.form-file-upload.method.manual }}` and paste the contents of the [certificate you got earlier](#azure-settings-begin).

  1. Click **{{ ui-key.yacloud_org.actions.add }}**.

{% endlist %}

## Complete the Azure app configuration {#azure-settings-end}

1. Navigate to the browser tab on which you were configuring SAML-based sign-on for the `yandex-cloud-saml` application.
1. Specify the redirect URL:

    1. Find the **Basic SAML configuration** section.
    1. In the section, click **Edit**.
    1. Specify the same redirect URL in both the **ID (entity)** and **Response URL (assertion consumer service URL)** fields.

        The redirect URL must be in the following format:

        ```text
        https://{{ auth-host }}/federations/<federation_ID>
        ```

        {% cut "How to get the federation ID" %}

        {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

        {% endcut %}

    1. Click **Save** in the right-hand panel.

1. (Optional) If you enabled **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** when [creating the federation](#create-federation) in {{ org-full-name }}, add the previously downloaded {{ yandex-cloud }} SAML certificate to the application:

    1. Find **SAML certificates** → **Verification certificates (optional)** and click **Edit**.
    1. Enable **Require verification certificates**.
    1. Click **Send certificate**.
    1. Upload the certificate in PEM format.
    
        If you did not download a SAML certificate when creating the federation, you can download it on the {{ org-full-name }} federation info page by clicking ![ArrowDownToLine](../../_assets/console-icons/arrow-down-to-line.svg) **{{ ui-key.yacloud_org.page.federation.action.download-cert }}** in the **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** field.
    1. Click **Save** in the right-hand panel.

1. Click **Save**.

## Configure group mapping on the Azure app side {#azure-mapping}

### Create a user {#create-user}

1. [Go to the Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **{{ microsoft-idp.entra-id-full }}**.
1. In the left-hand panel, select **Users** → **All users**.
1. Click **New user**. Select **Create new user** from the drop-down menu.
1. Go to the **Basics** tab.
1. In the **User principal name** field, enter a name for the user (e.g., `az_demo_user`) in combination with the domain (e.g., `example.com`).
1. In the **Mail nickname** field, specify an email address. By default, the nickname matches the username.

    You may specify a different nickname:

    1. Uncheck **Derive from user principal name**.
    1. Enter the mail nickname you prefer.

    For example, you can use `ivan_ivanov` for the `az_demo_user@example.com` user.

1. In the **Display name** field, enter a display name for the user that will appear in the interface, e.g., `Ivan Ivanov`.
1. In the **Password** field, provide the user password to be used for the first log in. By default, the password is generated automatically.

    You can specify the password manually:

    1. Uncheck **Auto-generate password**.
    1. Enter the password you prefer.

1. Make sure the **Account enabled** option is checked on the **Basics** tab.
1. Click **Review and create**.

### Create a group and add a user to it {#create-group}

1. [Go to the Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **{{ microsoft-idp.entra-id-full }}**.
1. Create a group:

    1. In the left-hand panel, select **Groups** → **All groups**.
    1. Click **Create group**.
    1. From the **Group type** drop-down list, select `Security group`.
    1. In the **Group name** field, enter a name for your group, e.g., `az_demo_group`.
    1. Under **Members**, click the **No members selected** link.
    1. In the window that opens, check the `az_demo_user@example.com` user and click **Select**.
    1. Click **Create**.

1. Get the ID of the group you created:

    1. In the left-hand panel, select **Groups** → **All groups**.
    1. Find `az_demo_group` in the list and copy its ID from the **Object ID** column.

        The ID has the following format: `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`.

### Configure access permissions for your group {#configure-azure-group-access}

Configure the application for the new group to have access to it.

1. [Go to the Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **Enterprise applications**.
1. On the left-hand panel, select **Enterprise applications** → **All applications**.
1. Select the `yandex-cloud-saml` application you created earlier.
1. On the left-hand panel, select **Users and groups**.
1. Click **Add user or group**.
1. In the **Groups** field, click **None selected**.
1. In the window that opens, check the `az_demo_group` group and click **Select**.
1. Click **Assign**.
1. Click **Save**.

### Configure group mapping {#map-azure-group}

1. [Go to the Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **Enterprise applications**.
1. On the left-hand panel, select **Enterprise applications** → **All applications**.
1. Select the `yandex-cloud-saml` application you created earlier.
1. In the left-hand panel, select **Single sign-on**.
1. Find the **Attributes and claims** section and click **Edit**. Next, you will configure the necessary claims.
1. Click **Add a group claim**.
1. Under **Which groups associated with the user should be returned in the claim?**, select `Security groups`.
1. Select `Group ID` from the **Source attribute** drop-down list.
1. Expand the **Advanced options** section and make the following changes:

    1. Enable **Change the name of the group claim**.
    1. In the **Name (optional)** field, enter `member`.

1. Click **Save** in the right-hand panel.
1. Click **Save**.

## Configure group mapping on the federation side {#org-mapping}

{% list tabs group=instructions %}

- {{ cloud-center }} interface {#cloud-center}

  1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. [Create a user group](../../organization/operations/create-group.md) named `yc-demo-group` in {{ org-name }} and [authorize it](../../organization/operations/access-group.md) to view resources in the cloud or a separate folder (the `viewer` role).

  1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Select `demo-federation` you created previously and navigate to the **{{ ui-key.yacloud_org.form.group-mapping.note.tab-idp }}** tab.

  1. Enable **{{ ui-key.yacloud_org.form.group-mapping.field.idp }}**.

  1. Click **{{ ui-key.yacloud_org.form.group-mapping.create.add }}**.

  1. In the **{{ ui-key.yacloud_org.form.group-mapping.note.group-name }}** field, enter the `az_demo_group` ID [you got in {{ microsoft-idp.entra-id-short }}](#create-group) earlier.

     {% note warning %}

     You selected group ID as the source attribute when [configuring group mapping on the Azure side](#map-azure-group).

     Therefore, enter the group ID, not its name.

     {% endnote %}

  1. In the **{{ ui-key.yacloud_org.form.group-mapping.note.iam-group }}** field, select the `yc-demo-group` group you created in {{ org-full-name }} from the list.

  1. Click **{{ ui-key.yacloud_org.actions.save-changes }}**.

- {{ TF }} {#tf}

  1. Describe the properties of the new resources in the {{ TF }} configuration file:

      ```hcl
      # Creating a user group
      resource "yandex_organizationmanager_group" "my-group" {
        name            = "yc-demo-group"
        organization_id = "demo-federation"
      }

      # Assigning the viewer role for a folder
      resource "yandex_resourcemanager_folder_iam_member" "viewers" {
        folder_id = "<folder_ID>"
        role      = "viewer"
        member    = "group:${yandex_organizationmanager_group.my-group.id}"
      }

      # Enabling federated user group mapping
      resource "yandex_organizationmanager_group_mapping" "my_group_map" {
        federation_id = "demo-federation"
        enabled       = true
      }

      # Configuring a federated user group mapping
      resource "yandex_organizationmanager_group_mapping_item" "group_mapping_item" {
        federation_id     = "demo-federation"
        internal_group_id = yandex_organizationmanager_group.my-group.id
        external_group_id = "<az_demo_group_ID>"

        depends_on = [yandex_organizationmanager_group_mapping.group_mapping]
      }
      ```

      Where:
      * `folder_id`: Folder the role is assigned for.
      * `external_group_id`: `az_demo_group` ID [you got in {{ microsoft-idp.entra-id-short }} earlier](#create-group).

         {% note warning %}

         You selected group ID as the source attribute when [configuring group mapping on the Azure side](#map-azure-group).

         Therefore, enter the group ID, not its name.

         {% endnote %}

      For more information, see [yandex_organizationmanager_group_mapping]({{ tf-provider-resources-link }}/organizationmanager_group_mapping) and [yandex_organizationmanager_group_mapping_item]({{ tf-provider-resources-link }}/organizationmanager_group_mapping_item) in the {{ TF }} provider documentation.

  1. Create the resources:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

{% endlist %}

## Test authentication {#test-auth}

1. Open your browser in guest or private browsing mode.

1. Use this URL to log in to the management console:

    ```text
    https://{{ console-host }}/federations/<federation_ID>
    ```

    {% cut "How to get the federation ID" %}

    {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

    {% endcut %}

    If you have set up everything correctly, the browser will redirect you to the authentication page in {{ microsoft-idp.entra-id-short }}.

1. Enter the credentials of the `az_demo_user@example.com` user [you created earlier in {{ microsoft-idp.entra-id-short }}](#create-user) and click **Sign in**.

    On successful authentication, the IdP server will redirect you to the `https://{{ auth-host }}/federations/<federation_ID>` URL you specified in the SAML settings for the Azure app and then to the [management console]({{ link-console-main }}) home page.

1. Make sure the signed in user belongs to `yc-demo-group` and has the viewer permissions for resources according to the role assigned to the group.
