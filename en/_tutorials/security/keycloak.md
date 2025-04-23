# User group mapping in {{ keycloak }}

To configure user group mapping in [{{ keycloak }}](https://www.keycloak.org/) and user groups in the [identity federation](../../organization/concepts/add-federation.md):

1. [Create a federation in {{ org-full-name }}](#create-federation).
1. [Add a {{ keycloak }} certificate to the federation](#add-certificate)
1. [Create and configure a SAML application in {{ keycloak }}](#keycloak-settings).
1. [Configure group mapping on the {{ keycloak }} side](#kc-mapping).
1. [Configure group mapping on the federation side](#org-mapping).
1. [Test authentication](#test-auth).

{% note info %}

All examples were tested with {{ keycloak }} version `21.1.2`.

{% endnote %}

## Getting started {#before-you-begin}

{% note tip %}

If you already have an active {{ keycloak }} server, check the {{ keycloak }} settings against the recommendations in this guide, and use your existing server instead of creating a new one. In this case, you can go directly to the [Configure group mapping on the {{ keycloak }}](#kc-mapping) side section.

{% endnote %}

1. Set up a local {{ keycloak }} server for testing:

    1. If you do not have Docker yet, [install it](https://docs.docker.com/get-docker/). Make sure Docker Engine is running.

    1. Install and run a Docker container with {{ keycloak }} version 21.1.2:

        ```bash
        docker run -p 8080:8080 \
        -e KEYCLOAK_ADMIN=admin \
        -e KEYCLOAK_ADMIN_PASSWORD=Pa55w0rd \
        quay.io/keycloak/keycloak:21.1.2 start-dev
        ```

    As long as the container is running, the {{ keycloak }} administrator account will be available at [http://localhost:8080/admin](http://localhost:8080/admin) or [http://0.0.0.0:8080/admin](http://0.0.0.0:8080/admin). The default login parameters are as follows:

    * **User name or email**: `admin`
    * **Password**: `Pa55w0rd`

    {% note info %}

    To enable employees on a corporate network or the internet to use {{ keycloak }} for authentication in your application, deploy the {{ keycloak }} IdP server on the network and set up a public address. For more information, see the [{{ keycloak }}](https://www.keycloak.org/server/hostname) documentation.

    {% endnote %}

1. Get the certificate used for signing in {{ keycloak }}:

    1. Log in to the {{ keycloak }} administrator account at: `http://<IP_or_URL_{{ keycloak }}>:8080/admin`.

        If you are using a local server from a Docker image, the default login credentials are:

        * URL: [http://0.0.0.0:8080/admin](http://0.0.0.0:8080/admin)
        * **User name or email**: `admin`
        * **Password**: `Pa55w0rd`

    1. In the **Realm Settings** section, select the **Keys** tab.

    1. In the **RS256** line, click **Certificate** and copy the certificate value.

    1. Save the certificate to a file named `keycloak-cert.cer` in the following format:

      ```text
      -----BEGIN CERTIFICATE-----
      <certificate_value>
      -----END CERTIFICATE-----
      ```

      You will need this certificate later when setting up the identity federation.

## Create a {{ org-full-name }} federation {#create-federation}

{% list tabs group=instructions %}

- {{ cloud-center }} interface {#cloud-center}

  1. Go to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. In the left-hand panel, select ![icon-federation](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Click ![Circles3Plus](../../_assets/console-icons/circles-3-plus.svg) **{{ ui-key.yacloud_org.form.federation.action.create }}** in the top-right corner of the page. In the window that opens:

      1. Enter a name for the federation, e.g., `demo-federation`. It must be unique within the folder.

      1. You can also add a description, if required.

      1. In the **{{ ui-key.yacloud_org.entity.federation.field.cookieMaxAge }}** field, specify the time before the browser asks the user to re-authenticate.

      1. In the **{{ ui-key.yacloud_org.entity.federation.field.issuer }}** field, enter a link in this format:

          ```text
          http://<{{ keycloak }}>_IP_or_URL:8080/realms/master
          ```

      1. In the **{{ ui-key.yacloud_org.entity.federation.field.ssoUrl }}** field, enter a link in this format:

          ```text
          http://<{{ keycloak }}>_IP_or_URL:8080/realms/master/protocol/saml
          ```

          {% include [ssourl_protocol](../../_includes/organization/ssourl_protocol.md) %}

      1. Enable **{{ ui-key.yacloud_org.entity.federation.field.autocreateUsers }}** to automatically add a new user to your organization after authentication. Otherwise, you will need to [manually add](../../organization/operations/add-account.md#add-user-sso) your federated users.

          {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

      1. (Optional) To make sure that all authentication requests from {{ yandex-cloud }} contain a digital signature, enable **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}**. You will need to install a {{ yandex-cloud }} SAML certificate on the IdP side.

          {% include [download-saml-cert-when-creating-fed](../../_includes/organization/download-saml-cert-when-creating-fed.md) %}

          {% include [setup-cert-for-idp](../../_includes/organization/setup-cert-for-idp.md) %}

          You will need this certificate later when setting up the client in {{ keycloak }}.

      1. {% include [forceauthn-option-enable](../../_includes/organization/forceauthn-option-enable.md) %}

      1. Click **{{ ui-key.yacloud_org.form.federation.create.action.create }}**.

{% endlist %}

## Add the {{ keycloak }} certificate to the federation {#add-certificate}

To make sure that {{ org-name }} can verify the {{ keycloak }} server certificate during authentication, add the certificate to the federation:

{% list tabs group=instructions %}

- {{ cloud-center }} interface {#cloud-center}

  1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Click the row with `demo-federation` to add your certificate to.

  1. Click **{{ ui-key.yacloud_org.page.federation.section.certificates }}** under **{{ ui-key.yacloud_org.entity.certificate.action.add }}** at the bottom of the page.

  1. Enter a name for the certificate and specify the path to the `keycloak-cert.cer` file.

  1. Click **{{ ui-key.yacloud_org.actions.add }}**.

{% endlist %}

{% include [federation-certificates-note](../../_includes/organization/federation-certificates-note.md) %}

## Create and configure a SAML application in {{ keycloak }} {#keycloak-settings}

A SAML application in {{ keycloak }} acts as an identity provider (IdP). To create and set up a SAML application:

1. Log in to the {{ keycloak }} administrator account at: `http://<IP_or_URL_{{ keycloak }}>:8080/admin`.

    If you are using a local server from a Docker image, the default login credentials are:

    * URL: [http://0.0.0.0:8080/admin](http://0.0.0.0:8080/admin)
    * **User name or email**: `admin`
    * **Password**: `Pa55w0rd`

1. Create a SAML application:

    1. In the left-hand panel, select **Clients**. Click **Create client**.

    1. In the **Client type** field, select **SAML**.

    1. In the **Client ID** field, enter the ACS URL to redirect users to after successful authentication.

        {% cut "How to get the federation ID" %}

        {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

        {% endcut %}

        
        {% cut "How to get the federation ACS URL" %}

        {% include [get-acs-url](../../_includes/organization/get-acs-url.md) %}

        {% endcut %}


    1. Click **Next**.
    1. Specify the ACS redirect URL,, in the following fields:

        * **Home URL**
        * **Valid Redirect URIs**
        * **IDP Initiated SSO Relay State**
    
        
        {% cut "How to get the federation ACS URL" %}

        {% include [get-acs-url](../../_includes/organization/get-acs-url.md) %}

        {% endcut %}


    1. Click **Save**.

1. Set up the SAML application parameters in the **Settings** tab:

    1. Enable the following options:

       * **Include AuthnStatement**
       * **Sign Assertions**
       * **Force name ID format**
       * **Force POST Binding**
       * **Front Channel Logout**

    1. In the **Signature Algorithm** field, select **RSA_SHA256**.

    1. In the **SAML Signature Key Name** field, select **CERT_SUBJECT**.

    1. Select `username` as **Name ID Format**.

    1. Click **Save**.

1. (Optional) If you enabled **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** when [creating the federation](#create-federation) in {{ org-full-name }}, set up digital signature verification in the SAML application:

    1. On the **Keys** tab of the SAML application, check that the **Client Signature Required** option is enabled.
    1. Click the **Import key** button under the automatically generated certificate and select **Certificate PEM** in the **Archive Format** field.
    1. Click **Browse** and select the {{ yandex-cloud }} SAML certificate you downloaded earlier to sign authentication requests.

        If you did not download a SAML certificate when creating the federation, you can download it on the {{ org-full-name }} federation info page by clicking ![ArrowDownToLine](../../_assets/console-icons/arrow-down-to-line.svg) **{{ ui-key.yacloud_org.page.federation.action.download-cert }}** in the **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** field.

    1. Click **Import**.
    1. Enable the **Encrypt Assertions** option.
    1. In the window that opens, select the **Generate** method and click **Confirm**.
    1. Click **Import key** under the generated certificate and select **Certificate PEM** in the **Archive Format** field.
    1. Click **Browse** and select the certificate you are going to use to sign authentication requests. The certificate is available for download on the {{ org-full-name }} federation info page in the **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}** field.
    1. Click **Import**.

## Configure group mapping on the {{ keycloak }} side {#kc-mapping}

1. Create a user:

    1. In the left-hand panel, select **Users**.
    1. Click **Add user** and enter a username, e.g., `demo_user1`.
    1. Click **Create**.
    1. In the **Credentials** tab, click **Set Password** and enter a password. For easier testing, disable the **Temporary** option.

1. Create a group and add a user to it:

    1. In the left-hand panel, select **Groups**.
    1. Click **Create group** and enter a name for the group, e.g., `kc_demo_group`.
    1. Click the group name, click **Add member** on the **Members** tab, and add the `demo_user1` user from the list to the group.

1. Add a mapper to the {{ keycloak }} application:

    1. In the left-hand panel, select **Clients** and select the previously created application from the list.
    1. Navigate to the **Client scopes** tab and select the ACS URL with the `-dedicated` postfix: `<ACS_URL>-dedicated`.

        
        {% cut "How to get the federation ACS URL" %}

        {% include [get-acs-url](../../_includes/organization/get-acs-url.md) %}

        {% endcut %}


    1. On the **Mappers** tab, click **Configure a new mapper**. Select **Group list** from the drop-down list.
    1. Specify the following mapper settings:

        * **Name**: `group_mapper`
        * **Group attribute name**: `member`
        * **SAML Attribute NameFormat**: `Basic`
        * **Single Group Attribute**: `On`
        * **Full group path**: `Off`

    1. Click **Save**.

## Configure group mapping on the federation side {#org-mapping}

{% list tabs group=instructions %}

- {{ cloud-center }} interface {#cloud-center}

  1. Log in to [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. [Create a user group](../../organization/operations/create-group.md) named `yc_demo_group` in {{ org-name }} and [authorize it](../../organization/operations/access-group.md) to view resources in the cloud or a separate folder (the `viewer` role).

  1. In the left-hand panel, select ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Select `demo-federation` you created previously and navigate to the **{{ ui-key.yacloud_org.form.group-mapping.note.tab-idp }}** tab.

  1. Enable **{{ ui-key.yacloud_org.form.group-mapping.field.idp }}**.

  1. Click **{{ ui-key.yacloud_org.form.group-mapping.create.add }}**.

  1. In the **{{ ui-key.yacloud_org.form.group-mapping.note.group-name }}** field, enter the group name in {{ keycloak }}: `kc_demo_group`.

  1. In the **{{ ui-key.yacloud_org.form.group-mapping.note.iam-group }}** field, select the `yc_demo_group` group you created in {{ org-full-name }} from the list.

  1. Click **{{ ui-key.yacloud_org.actions.save-changes }}**.

- {{ TF }} {#tf}

  1. Describe the properties of the new resources in the {{ TF }} configuration file:

      ```hcl
      # Creating a user group
      resource "yandex_organizationmanager_group" "my-group" {
        name            = "yc_demo_group"
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
        external_group_id = "kc_demo_group"

        depends_on = [yandex_organizationmanager_group_mapping.group_mapping]
      }
      ```

      Where:
      * `folder_id`: Folder the role is assigned for.

      For more information, see [yandex_organizationmanager_group_mapping]({{ tf-provider-resources-link }}/organizationmanager_group_mapping) and [yandex_organizationmanager_group_mapping_item]({{ tf-provider-resources-link }}/organizationmanager_group_mapping_item) in the {{ TF }} provider documentation.

  1. Create the resources:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

{% endlist %}

## Test authentication {#test-auth}

1. Open your browser in guest or private browsing mode.

1. Use this URL to log in to the management console:

    ```text
    {{ link-console-main }}/federations/<federation_ID>
    ```

    {% cut "How to get the federation ID" %}

    {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

    {% endcut %}

    If you have set up everything correctly, the browser will redirect you to the authentication page in {{ keycloak }}.

1. Enter the username and password for the test federated user (`demo_user1`) and click **Sign in**.

    On successful authentication, the IdP server will redirect you to the ACS URL you specified in the {{ keycloak }} settings and then to the [management console]({{ link-console-main }}) home page.

1. Make sure the created `demo_user1` belongs to `yc_demo_group` and has the viewer permissions for resources according to the role assigned to the group.
