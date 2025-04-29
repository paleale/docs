The `organization-manager.editor` role enables managing the organization settings, identity federations, users, and user groups.

{% cut "Users with this role can:" %}

* View and edit info on the relevant [organization](../../organization/concepts/organization.md) under Cloud Organization.
* View and edit organization settings.
* View info on the [access permissions](../../iam/concepts/access-control/index.md) granted for the organization.
* View info on the [identity federations](../../organization/concepts/add-federation.md) in an organization and create, modify, and delete such federations.
* View the list of the organization [users](../../overview/roles-and-resources.md#users).
* Add and remove federated users.
* View the list of the organization users that are [subscribed](../../organization/operations/subscribe-user-for-notifications.md) to technical notifications on organization events, as well as edit this list.
* View info on the [certificates](../../organization/concepts/add-federation.md#build-trust) and add, modify, and delete them.
* View the lists of [federated user](../../iam/concepts/users/accounts.md#saml-federation) group [mappings](../../organization/concepts/add-federation.md#group-mapping) and info on them, as well as create, edit, and delete such lists.
* View info on the federated user attributes, as well as create, modify, and delete them.
* View info on the organization's [OS Login](../../organization/concepts/os-login.md) settings.
* View the list of OS Login [profiles](../../organization/concepts/os-login.md#os-login-profiles) for users and service accounts.
* View the list of the organization users' SSH keys and the info on such keys.
* View info on [user groups](../../organization/concepts/groups.md), as well as create, modify, and delete them.
* View info on access permissions granted for user groups.
* View the list of groups a certain user is a member of, as well as the list of users that are members of a certain group.
* View and edit the [refresh token](../../iam/concepts/authorization/refresh-token.md) settings in an organization.
* View the info on the refresh tokens of the organization’s federated users.
* View info on Cloud Organization quotas.
* View the info on the effective tech support [service plan](../../support/pricing.md#effective-plans).
* View the list of technical support [requests](../../support/overview.md) and the info on them, as well as create and close such requests, leave comments, and attach files to them.

{% endcut %}

This role also includes the `organization-manager.viewer` permissions.