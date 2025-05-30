---
editable: false
sourcePath: en/_cli-ref/cli-ref/cic/cli-ref/trunk-connection/create.md
---

# yc cic trunk-connection create

Create a trunk connection.

#### Command Usage

Syntax: 

`yc cic trunk-connection create <TRUNK-CONNECTION-NAME> [Flags...] [Global Flags...]`

#### Flags

| Flag | Description |
|----|----|
|`--name`|<b>`string`</b><br/>Name of the trunkConnection.|
|`--description`|<b>`string`</b><br/>Description of the trunkConnection.|
|`--labels`|<b>`key=value[,key=value...]`</b><br/>A list of trunkConnection labels as key-value pairs.|
|`--region`|<b>`string`</b><br/>Region of the trunkConnection.|
|`--deletion-protection`|Flag protecting the trunkConnection from deletion.|
|`--pop`|<b>`string`</b><br/>Point of presence id where the trunkConnection is located.|
|`--capacity`|<b>`string`</b><br/>Capacity of the trunkConnection.|
|`--trunk-options`|<b>`key=value[,key=value...]`</b><br/>A list options of the trunkConnection as key-value pairs.|
|`--async`|Display information about the operation in progress, without waiting for the operation to complete.|

#### Global Flags

| Flag | Description |
|----|----|
|`--profile`|<b>`string`</b><br/>Set the custom configuration file.|
|`--debug`|Debug logging.|
|`--debug-grpc`|Debug gRPC logging. Very verbose, used for debugging connection problems.|
|`--no-user-output`|Disable printing user intended output to stderr.|
|`--retry`|<b>`int`</b><br/>Enable gRPC retries. By default, retries are enabled with maximum 5 attempts.<br/>Pass 0 to disable retries. Pass any negative value for infinite retries.<br/>Even infinite retries are capped with 2 minutes timeout.|
|`--cloud-id`|<b>`string`</b><br/>Set the ID of the cloud to use.|
|`--folder-id`|<b>`string`</b><br/>Set the ID of the folder to use.|
|`--folder-name`|<b>`string`</b><br/>Set the name of the folder to use (will be resolved to id).|
|`--endpoint`|<b>`string`</b><br/>Set the Cloud API endpoint (host:port).|
|`--token`|<b>`string`</b><br/>Set the OAuth token to use.|
|`--impersonate-service-account-id`|<b>`string`</b><br/>Set the ID of the service account to impersonate.|
|`--no-browser`|Disable opening browser for authentication.|
|`--format`|<b>`string`</b><br/>Set the output format: text (default), yaml, json, json-rest.|
|`--jq`|<b>`string`</b><br/>Query to select values from the response using jq syntax|
|`-h`,`--help`|Display help for the command.|
