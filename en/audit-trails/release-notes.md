---
title: '{{ at-full-name }} release notes'
description: This section contains {{ at-name }} release notes.
---

# {{ at-full-name }} release notes

## Q1 2025 {#q1-2025}

* Added new events for the services:

  {% cut "{{ baremetal-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `BatchCreateServer` | Leasing several {{ baremetal-name }} [servers](../baremetal/concepts/servers.md) at the same time
  `CreateExternalConnection` | Creating a [private connection](../baremetal/concepts/network.md#private-connection-to-vpc) to subnets in a VPC or on-prem infrastructure
  `CreatePrivateSubnet` | Creating a [private subnet](../baremetal/concepts/network.md#private-subnet)
  `CreateServer` | Leasing a {{ baremetal-name }} server
  `CreateVRF` | Creating a [virtual network segment](../baremetal/concepts/network.md#vrf-segment) (VRF)
  `DeleteExternalConnection` | Deleting a private connection to subnets in a VPC or on-prem infrastructure
  `DeletePrivateSubnet` | Deleting a private subnet
  `DeleteVRF` | Deleting a virtual network segment (VRF)
  `PowerOffServer` | Powering off a {{ baremetal-name }} server
  `PowerOnServer` | Powering on a {{ baremetal-name }} server
  `RebootServer` | Restarting a {{ baremetal-name }} server
  `RegisterServerBackupAgent` | Registering a {{ backup-full-name }} [agent](../backup/concepts/agent.md) on a {{ baremetal-name }} server
  `ReinstallServer` | Reinstalling a {{ baremetal-name }} server OS
  `StartServerProlongation` | Enabling auto-renewal of {{ baremetal-name }} server lease
  `StopServerProlongation` | Disabling auto-renewal of {{ baremetal-name }} server lease
  `UpdatePrivateSubnet` | Updating a private subnet
  `UpdateServer` | Updating a {{ baremetal-name }} server
  `UpdateVRF` | Updating a virtual network segment (VRF)

  {% endcut %}

  {% cut "{{ backup-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `DeleteArchive` | Deleting a backup archive

  {% endcut %}

  {% cut "{{ cdn-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `gcore.ResourceDelete` | Deleting a resource
  `gcore.ResourceRuleCreate` | Creating a Rewrite rule
  `gcore.ResourceRuleDelete` | Deleting a Rewrite rule
  `gcore.ResourceRuleUpdate` | Updating a Rewrite rule

  {% endcut %}

  {% cut "{{ ml-platform-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateFilestore` | Create file storage
  `CreateModel` | Creating a [model](../datasphere/concepts/models/index.md)
  `CreateS3Connector` | Creating an [S3 connector](https://yandex.ru/support/metrica/pro/cloud.html)
  `CreateSecret` | Creating a [secret](../datasphere/concepts/secrets.md)
  `CreateSparkConnector` | Creating a [Spark connector](../datasphere/concepts/spark-connector.md)
  `CreateYandexDataProcessing` | Creating a [{{ dataproc-name }} template](../datasphere/concepts/data-processing-template.md)
  `DeleteFilestore` | Delete file storage
  `DeleteModel` | Delete model
  `DeleteS3Connector` | Deleting an S3 connector
  `DeleteSecret` | Destroying a secret
  `DeleteSparkConnector` | Deleting a Spark connector
  `DeleteYandexDataProcessing` | Deleting a {{ dataproc-name }} template
  `UpdateFilestore` | Updating a file storage
  `UpdateModel` | Edit model
  `UpdateS3Connector` | Updating an S3 connector
  `UpdateSecret` | Updating a secret
  `UpdateSparkConnector` | Updating a Spark connector
  `UpdateYandexDataProcessing` | Updating a {{ dataproc-name }} template

  {% endcut %}

  {% cut "{{ cloud-desktop-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateDesktop` | Creating a desktop
  `CreateDesktopGroup` | Creating a desktop group
  `DeleteDesktop` | Deleting a desktop
  `DeleteDesktopGroup` | Deleting a desktop group
  `RestartDesktop` | Restarting a desktop

  {% endcut %}

  {% cut "{{ video-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `BatchDeleteChannels` | Deleting a channel group
  `BatchDeleteEpisodes` | Deleting an episode group
  `BatchDeletePlaylists` | Deleting a playlist group
  `BatchDeleteStreamLines` | Deleting a group of stream lines
  `BatchDeleteStreams` | Deleting a stream group
  `BatchDeleteVideos` | Deleting a video group
  `CreateChannel` | Creating a channel
  `CreateEpisode` | Creating an episode
  `CreatePlaylist` | Creating a playlist
  `CreateStream` | Creating a stream
  `CreateStreamLine` | Creating a stream line
  `CreateSubtitle` | Subtitling
  `CreateThumbnail` | Creating a thumbnail
  `CreateVideo` | Creating a video
  `DeleteChannel` | Deleting a channel
  `DeleteEpisode` | Deleting an episode
  `DeletePlaylist` | Deleting a playlist
  `DeleteStream` | Deleting a stream
  `DeleteStreamLine` | Deleting a stream line
  `DeleteSubtitle` | Deleting subtitles
  `DeleteThumbnail` | Deleting a thumbnail
  `DeleteVideo` | Deleting a video
  `EpisodePerformAction` | Performing actions with an episode
  `SetChannelAccessBindings` | Assigning access permissions for a channel
  `StreamLinePerformAction` | Performing actions with a stream line
  `StreamLineUpdateStreamKey` | Updating a stream key of a stream line
  `StreamPerformAction` | Performing actions with a stream
  `TranscodeVideo` | Transcoding a video
  `UpdateChannel` | Updating a channel
  `UpdateChannelAccessBindings` | Updating access permissions for a channel
  `UpdateEpisode` | Updating an episode
  `UpdatePlaylist` | Updating a playlist
  `UpdateStream` | Updating a stream
  `UpdateStreamLine` | Updating a stream line
  `UpdateVideo` | Updating a video
  `VideoPerformAction` | Performing actions with a video

  {% endcut %}

  {% cut "{{ compute-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateReservedInstancePool` | Creating a pool of reserved VMs
  `DeleteReservedInstancePool` | Deleting a pool of reserved VMs
  `UpdateReservedInstancePool` | Updating a pool of reserved VMs

  {% endcut %}

  {% cut "{{ iam-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `RevokeLeakedCredential` | Revoking a compromised secret
  `workload.oidc.SetFederationAccessBindings` | Assigning access permissions to a workload identity federation
  `workload.oidc.UpdateFederationAccessBindings` | Modifying access permissions of a workload identity federation

  {% endcut %}
  
  {% cut "{{ mch-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `RestartClusterHosts` | Restarting cluster hosts

  {% endcut %}

  {% cut "{{ mgl-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateRunner` | Creating {{ GLR }}
  `DeleteRunner` | Deleting {{ GLR }}
  `StartRunner` | Starting {{ GLR }}
  `StopRunner` | Stopping {{ GLR }}
  `UpdateRunner` | Updating {{ GLR }}

  {% endcut %}

  {% cut "{{ managed-k8s-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `AutoUpgradeCluster` | Performing cluster auto-updates
  `AutoUpgradeNodeGroup` | Performing node group auto-updates
  `CreateCluster` | Creating a cluster
  `CreateNodeGroup` | Creating a node group
  `DeleteCluster` | Deleting a cluster
  `DeleteNodeGroup` | Deleting a node group
  `DeleteStoppedCluster` | Deleting a stopped cluster
  `InstallHelmRelease` | Installing a Helm version
  `SetClusterAccessBindings` | Assigning access permissions for a cluster
  `StartCluster` | Starting a cluster
  `StopCluster` | Stopping a cluster
  `UninstallHelmRelease` | Destroying a Helm version
  `UpdateCluster` | Updating a cluster
  `UpdateClusterAccessBindings` | Updating access permissions for a cluster
  `UpdateHelmRelease` | Updating a Helm version
  `UpdateNodeGroup` | Updating a  node group

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `apiserver.ApiServerApprove` | Confirming a request
  `apiserver.ApiServerBind` | Linking a role
  `apiserver.ApiServerCreate` | Creating a resource
  `apiserver.ApiServerDelete` | Deleting a resource
  `apiserver.ApiServerDeleteCollection` | Deleting a resource collection
  `apiserver.ApiServerEscalate` | Escalating privileges
  `apiserver.ApiServerGet` | Getting information about a resource
  `apiserver.ApiServerHead` | Getting resource metadata
  `apiserver.ApiServerImpersonate` | Impersonation
  `apiserver.ApiServerList` | Getting information about a resource collection
  `apiserver.ApiServerNonstandardVerb` | The event is generated if the {{ managed-k8s-name }} audit log contains a non-standard value in the `verb` field
  `apiserver.ApiServerOptions` | Configuring a resource
  `apiserver.ApiServerPatch` | Changing a resource
  `apiserver.ApiServerPost` | Creating a resource
  `apiserver.ApiServerPut` | Updating a resource
  `apiserver.ApiServerUpdate` | Updating a query
  `apiserver.ApiServerWatch` | Tracking resources
  
  {% endcut %}

  {% cut "{{ mos-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `RestartOpenSearch` | Restarting a cluster
  `SwitchMaster` | Changing the host quorum leader with the `MANAGER` role

  {% endcut %}

  {% cut "{{ mrd-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `DeleteBackup` | Deleting backups

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `CreateUser` | Creating a user in a cluster
  `DeleteUser` | Deleting a user from a cluster
  `UpdateUser` | Updating a user in a cluster

  {% endcut %}

  {% cut "{{ vpc-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreatePrivateEndpoint` | Creating a service connection
  `DeletePrivateEndpoint` | Deleting a service connection
  `UpdatePrivateEndpoint` | Updating a service connection

  {% endcut %}

  {% cut "{{ billing-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `BillingAccountCreate` | Creating a billing account
  `BillingAccountUpdate` | Updating a billing account

  {% endcut %}

  {% cut "{{ lockbox-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `GetPayload` | Accessing the contents of a secret
  `GetPayloadEx` | Accessing the contents of a secret by folder or name

  {% endcut %}

  {% cut "{{ metadata-hub-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `CreateCluster` | Creating a {{ metastore-full-name }} [cluster](../metadata-hub/concepts/metastore.md)
  `DeleteCluster` | Deleting a {{ metastore-full-name }} cluster
  `StartCluster` | Starting a {{ metastore-full-name }} cluster
  `StopCluster` | Stopping a {{ metastore-full-name }} cluster
  `UpdateCluster` | Updating an {{ metastore-full-name }} cluster

  {% endcut %}

## Q4 2024 {#q4-2024}

* Added new events for the services:

  {% cut "{{ org-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `AttachRegion` | Connecting a region

  {% endcut %}

  {% cut "{{ ml-platform-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `ActivateDataset` | Activating a dataset
  `ActivateDocker` | Applying a Docker image to a project
  `CreateDataset` | Creating a dataset
  `CreateDocker` | Creating a Docker image
  `DeactivateDataset` | Deactivating a dataset
  `DeleteDataset` | Deleting a dataset
  `DeleteDocker` | Deleting a Docker image

  {% endcut %}

  {% cut "{{ mos-short-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `DeleteBackup` | Deleting backups

  {% endcut %}

  {% cut "{{ objstorage-name }} " %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `PresignURLCreate` | Creating a signed link

  {% endcut %}


## Q3 2024 {#q3-2024}

* Added the option of filtering by event type. For more information, see [Trail settings](./concepts/trail.md#trail-settings).
* Added new events for the services:

  {% cut "{{ certificate-manager-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event name | Description
  --- | ---
  `GetCertificateContent` | Getting the contents of an SSL certificate

  {% endcut %}

  {% cut "{{ org-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `DeleteFederatedUserAccounts` | Deleting a user from a federation

  {% endcut %}

  {% cut "{{ ml-platform-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CloseProject` | Closing a project
  `CreateNode` | Creating a node
  `DeleteNode` | Deleting a node
  `OpenProject` | Opening a project
  `ResumeNode` | Resuming a node
  `SuspendNode` | Suspending a node
  `UpdateNode` | Updating a node

  {% endcut %}

  {% cut "{{ iam-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `workload.CreateFederatedCredential` | Creating [a link](../iam/concepts/workload-identity.md#federated-credentials) in a service account federation
  `workload.DeleteFederatedCredential` | Deleting a link from a service account federation
  `workload.oidc.CreateFederation` | Creating a [workload identity federation](../iam/concepts/workload-identity.md)
  `workload.oidc.DeleteFederation` | Deleting a workload identity federation
  `workload.oidc.UpdateFederation` | Updating a workload identity federation

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `RevokeIamToken` | Revoking an IAM token

  {% endcut %}

  {% cut "{{ load-testing-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateRegressionDashboard` | Creating a regression dashboard
  `DeleteRegressionDashboard` | Deleting a regression dashboard
  `UpdateRegressionDashboard` | Editing a regression dashboard

  {% endcut %}

  {% cut "{{ maf-name }}" %}

  [Management events](./concepts/format.md):

  Event name | Description
  --- | ---
  `CreateCluster` | Creating a cluster
  `DeleteCluster` | Deleting a cluster
  `StartCluster` | Starting a cluster
  `StopCluster` | Stopping a cluster
  `UpdateCluster` | Updating a cluster

  {% endcut %}

  {% cut "{{ mch-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `AddClusterShard` | Adding a shard to a cluster
  `DeleteClusterShard` | Deleting a shard from a cluster

  {% endcut %}

  {% cut "{{ mgp-name }}" %}

  [Management events](./concepts/format.md):

  Event name | Description
  --- | ---
  `MoveCluster` | Moving a cluster

  {% endcut %}

  {% cut "{{ managed-k8s-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event name | Description
  --- | ---
  `ApiServerAuditEvent` | {{ managed-k8s-name }} cluster audit event

  {% endcut %}

  {% cut "{{ mmy-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event name | Description
  --- | ---
  `DatabaseUserSQLRequest` | User SQL query to a database

  {% endcut %}

  {% cut "{{ sd-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event name | Description
  --- | ---
  `ComputeNodeAccess` | Connecting the {{ atr-name }} [module](../security-deck/concepts/access-transparency.md) to the {{ dataproc-name }} [subcluster](../data-proc/concepts/index.md#resources)
  `MDBClusterAccess` | Connecting the {{ atr-name }} module to a database cluster

  {% endcut %}

  {% cut "{{ sws-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateArlProfile` | Creating an ARL profile
  `CreateSecurityProfile` | Creating a security profile
  `CreateWafProfile` | Creating a WAF profile
  `DeleteArlProfile` | Deleting an ARL profile
  `DeleteSecurityProfile` | Deleting a security profile
  `DeleteWafProfile` | Deleting a WAF profile
  `UpdateArlProfile` | Updating an ARL profile
  `UpdateSecurityProfile` | Updating a security profile
  `UpdateWafProfile` | Updating a WAF profile

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `ArlMatchedRequest` | Request according to the ARL rules
  `WafMatchedExclusionRule` | Applied WAF exclusion rule
  `WafMatchedRule` | Applied WAF rule

  {% endcut %}

  {% cut "{{ postbox-name }}" %}

  [Management events](./concepts/format.md):

  Event name | Description
  --- | ---
  `CreateConfigurationSet` | Creating a [configuration](../postbox/concepts/glossary.md#configuration)
  `CreateIdentity` | Creating an address
  `DeleteConfigurationSet` | Deleting a configuration
  `DeleteIdentity` | Deleting an address
  `UpdateConfigurationSet` | Updating a configuration
  `UpdateIdentity` | Updating an address

  {% endcut %}

## Q2 2024 {#q2-2024}

* Added new events for the services:

  {% cut "{{ api-gw-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `SetApiGatewayAccessBindings` | Setting access bindings for an API gateway
  `UpdateApiGatewayAccessBindings` | Updating access bindings for an API gateway

  {% endcut %}

  {% cut "{{ backup-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `InitResource` | Initializing a VM connection to {{ backup-name }}
  `UpdateResource`| Updating the status of VM connection to {{ backup-name }}

  {% endcut %}

  {% cut "{{ certificate-manager-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `SetCertificateAccessBindings` | Setting access bindings for a certificate
  `SetDomainPrimaryCertificate` | Assigning a primary certificate to a domain

  {% endcut %}

  {% cut "{{ compute-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `AttachInstanceNetworkInterface` | Connecting a network interface
  `DetachInstanceNetworkInterface` | Disconnecting a network interface

  {% endcut %}

  {% cut "{{ ml-platform-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CloneJob` | Cloning a job
  `UpdateJobDataTtl` | Updating job data lifetime

  {% endcut %}

  {% cut "{{ sf-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `mdbproxy.SetProxyAccessBindings` | Setting access bindings for a managed database
  `mdbproxy.UpdateProxyAccessBindings` | Updating access bindings for a managed database
  `triggers.PauseTrigger` | Pausing a trigger
  `triggers.ResumeTrigger` | Resuming a trigger

  {% endcut %}

  {% cut "{{ iam-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `SetServiceAccountAccessBindings` | Setting access bindings for a service account

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `CreateIamToken` | Creating an IAM token
  `oslogin.CheckSshPolicy` | Checking permissions to connect via SSH with {{ oslogin }} access
  `oslogin.GenerateSshCertificate` | Generating an SSH certificate for {{ oslogin }} access

  {% endcut %}

  {% cut "{{ mch-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `DeleteBackup` | Deleting backups

  {% endcut %}

  {% cut "{{ mgl-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `FinishMigration` | Completing migration of an instance to another availability zone
  `PrepareBackupUpload` | Preparing for recovery from a backup
  `RollbackMigration` | Canceling migration of an instance to another availability zone
  `StartMigration` | Starting migration of an instance to another availability zone

  {% endcut %}

  {% cut "{{ mpg-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `DatabaseUserSQLRequest`| User SQL query to a database

  {% endcut %}

  {% cut "{{ mrd-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `EnableClusterSharding` | Enabling cluster sharding

  {% endcut %}

  {% cut "{{ marketplace-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `ActivateSubscriptionAutoRenewal` | Activating automatic subscription renewal
  `CancelSubscriptionAutoRenewal` | Canceling automatic subscription renewal

  {% endcut %}

  {% cut "{{ speechsense-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateProject` | Creating a project
  `CreateSpace` | Creating a space
  `DeleteProject` | Deleting a project
  `DeleteSpace` | Deleting a space
  `SetProjectAccessBindings` | Setting access bindings for a project
  `SetSpaceAccessBindings` | Setting access bindings for a space
  `UpdateProject` | Updating a project
  `UpdateProjectAccessBindings` | Updating access bindings for a project
  `UpdateSpace` | Editing a space
  `UpdateSpaceAccessBindings` | Updating access bindings for a space

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `CreateTalksReport` | Creating a dialog report
  `GetTalk` | Getting a dialog
  `GetTalkAudio` | Getting an audio recording of a dialog
  `SearchTalks` | Searching for a dialog
  `UploadTalkToConnection` | Uploading a dialog to a connection

  {% endcut %}

  {% cut "{{ websql-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateSavedQuery` | Saving a query
  `DeleteExecutedQuery` | Deleting an executed query
  `DeleteSavedQuery` | Deleting a saved query
  `EditSavedQuery` | Editing a saved query
  `PublishExecutedQuery` | Publishing an executed query
  `PublishSavedQuery` | Publishing a saved query

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `Execute` | Running a query
  `GenerateSql` | Generating a query
  `GetDatabaseStructure` | Getting database structure

  {% endcut %}

  {% cut "{{ wiki-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `AccessRequestCreate` | Creating a page access request
  `AccessRequestProcess` | Processing a page access request
  `AttachmentCreate` | Uploading a file to a page
  `AttachmentDelete` | Deleting a file from a page
  `AuthorRoleRequestCreate` | Creating a request for adding to page authors
  `AuthorRoleRequestProcess` | Processing a request for adding to page authors
  `BookmarkCreate` | Adding to favorites
  `BookmarkDelete` | Deleting from favorites
  `BookmarkTagsCreate` | Creating tags for favorites
  `BookmarkTagsDelete` | Deleting tags for favorites
  `BookmarkUpdate` | Updating tags for a page in favorites
  `ClusterMove` | Moving a page cluster
  `CommentCreate` | Creating a comment
  `CommentDelete` | Deleting a comment
  `CommentUpdate` | Updating a comment
  `GridAddColumns` | Adding columns to a dynamic table
  `GridAddRows` | Adding rows to a dynamic table
  `GridClone` | Creating a dynamic table copy
  `GridCreate` | Creating a new dynamic table
  `GridDelete` | Deleting a dynamic table
  `GridImport` | Importing a dynamic table from a file
  `GridMoveColumns` | Moving a dynamic table column
  `GridMoveRows` | Moving a dynamic table row
  `GridRemoveColumns` | Deleting dynamic table columns
  `GridRemoveRows` | Deleting dynamic table rows
  `GridRollback` | Undoing changes in a dynamic table
  `GridUpdate` | Updating a dynamic table
  `GridUpdateCells` | Updating cells in a dynamic table
  `GridUpdateColumn` | Updating a dynamic table column
  `GridUpdateRow` | Updating a dynamic table row
  `PageChangeOrder` | Changing page order
  `PageChangeToYFM` | Converting a page to a new editor
  `PageClone` | Creating a page copy
  `PageCreate` | Creating a page
  `PageDelete` | Deleting a page
  `PageImport` | Importing a page from a file
  `PageRecover` | Recovering a deleted page
  `PageUpdate`| Updating a page
  `RecoveryTokenDelete` | Deleting a page recovery token
  `SubscriberDelete` | Deleting a subscriber
  `SubscribersAdd` | Adding subscribers
  `SubscriptionAdd` | Adding a subscription
  `SubscriptionDelete` | Deleting a subscription

  {% endcut %}

* Replaced the `impersonator_info` field with `token_info` in the `authentication` section.

## Q1 2024 {#q1-2024}

* Added new events for the services:

  {% cut "{{ certificate-manager-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `UpdateDomainAccessBindings` | Updating access permissions for a domain

  {% endcut %}

  {% cut "{{ cloud-apps-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateCloudApplication` | Creating an application
  `DeleteCloudApplication` | Deleting an application
  `UpdateCloudApplication` | Updating an application

  {% endcut %}

  {% cut "{{ dns-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `UpdatePrivateNetworks` | Updating private networks

  {% endcut %}

  {% cut "{{ cloud-logging-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `ChangeLogGroupAccessBindings` | Updating access permissions for a log group
  `CreateExport` | Creating a log export
  `CreateLogGroup` | Creating a log group
  `CreateSink` | Creating a log sink
  `UpdateLogGroup` | Updating a log group
  `DeleteExport` | Deleting a log export
  `DeleteLogGroup` | Deleting a log group
  `DeleteSink` | Deleting a log sink
  `SetExportAccessBindings` | Assigning access permissions for a log export
  `SetLogGroupAccessBindings` | Assigning access permissions for a log group
  `SetSinkAccessBindings` | Assigning access permissions for a log sink
  `UpdateExport` | Updating a log export
  `UpdateExportAccessBindings` | Updating access permissions for a log export
  `UpdateLogGroupAccessBindings` | Updating access permissions for a log group
  `UpdateSink` | Updating a log sink
  `UpdateSinkAccessBindings` | Updating access permissions for a log sink

  {% endcut %}

  {% cut "{{ org-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateOsLoginProfile` | Creating a profile {{ oslogin }}
  `CreateUserSshKey` | Creating an SSH user key
  `UpdateOsLoginProfile` | Updating an {{ oslogin }} profile
  `UpdateOsLoginSettings` | Editing {{ oslogin }} settings
  `UpdateUserSshKey` | Updating an SSH user key
  `SetDefaultProfile` | Setting the default profile
  `DeleteOsLoginProfile` | Deleting an {{ oslogin }} profile
  `DeleteUserSshKey` | Deleting an SSH user key

  {% endcut %}

  {% cut "{{ compute-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateHostGroup` | Creating a group of dedicated hosts
  `DeleteHostGroup` | Deleting a group of dedicated hosts
  `UpdateHostGroup` | Updating a group of dedicated hosts
  `serialssh.ConnectSerialPort` | Connecting to an instance serial port

  {% endcut %}

  {% cut "{{ kms-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `asymmetricencryption.CancelAsymmetricEncryptionKeyDeletion` | Canceling the deletion of an asymmetric encryption key pair
  `asymmetricsignature.CancelAsymmetricSignatureKeyDeletion` | Canceling the deletion of a digital signature key pair

  {% endcut %}

  {% cut "{{ load-testing-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateAgent` | Creating an agent
  `CreateConfig` | Creating a configuration
  `CreateMigration` | Creating a migration
  `CreateTest` | Creating a test
  `DeleteAgent` | Deleting an agent
  `DeleteConfig` | Deleting a configuration
  `DeleteTest` | Deleting a test
  `RestartAgent` | Restarting an agent
  `StartAgent` | Running an agent
  `StopAgent` | Stopping an agent
  `StopTest` | Stopping a test
  `UpdateAgent` | Updating an agent
  `UpdateTest` | Updating a test
  `UpgradeImageAgent` | Updating an agent image

  {% endcut %}

  {% cut "{{ mgl-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateInstanceBackup` | Creating an instance backup
  `ReconfigureGitlab` | Editing the GitLab configuration
  `RescheduleMaintenance` | Changing scheduled maintenance date and time
  `ScheduleUpgrade` | Setting the instance upgrade time

  {% endcut %}

  {% cut "{{ mch-short-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `UpdateClusterExternalDictionary` | Editing an external dictionary

  {% endcut %}


  {% cut "{{ mgp-short-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `CreateHBARule` | Creating a user authentication rule
  `CreatePXFDatasource` | Creating a connection to an external table
  `DeleteBackup` | Deleting backups
  `DeleteHBARule` | Deleting a user authentication rule
  `DeletePXFDatasource` | Deleting an external table connection
  `UpdateHBARule` | Updating a user authentication rule
  `UpdatePXFDatasource` | Updating an external table connection

  {% endcut %}

  {% cut "{{ mmg-short-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `DeleteBackup` | Deleting backups

  {% endcut %}

  {% cut "{{ mmy-short-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `DatabaseUserLogin` | Connecting a user to a database
  `DatabaseUserLogout` | Disconnecting a user from a database

  {% endcut %}

  {% cut "{{ mos-short-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `MoveCluster` | Moving a cluster
  `UpdateDashboardsNodeGroup` | Updating a `Dashboards` type host group

  {% endcut %}

  {% cut "{{ mpg-short-name }}" %}

  [Data events](./concepts/format-data-plane.md):

  Event | Description
  --- | ---
  `DatabaseUserLogin` | Connecting a user to a database
  `DatabaseUserLogout` | Disconnecting a user from a database
  `StartClusterFailover` | Launching master switching for a cluster

  {% endcut %}

  {% cut "{{ vpc-name }}" %}

  [Management events](./concepts/format.md):

  Event | Description
  --- | ---
  `RelocateSubnet` | Moving a cloud subnet to a different availability zone

  {% endcut %}

* Fixed an error that delayed event delivery with the trail in the `Error` status.

* Modified the mechanism for delivering database management events and database user management events from {{ mpg-full-name }}, {{ mmy-full-name }}, and {{ mmg-full-name }}. Now they are considered [data events](./concepts/format-data-plane.md).