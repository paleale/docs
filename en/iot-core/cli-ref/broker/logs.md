---
editable: false
sourcePath: en/_cli-ref/cli-ref/iot/cli-ref/broker/logs.md
---

# yc iot broker logs

Show logs for the specified broker

#### Command Usage

Syntax: 

`yc iot broker logs <BROKER-NAME>|<BROKER-ID> [SINCE] [FILTER] [Flags...] [Global Flags...]`

#### Flags

| Flag | Description |
|----|----|
|`--id`|<b>`string`</b><br/>Broker id.|
|`--name`|<b>`string`</b><br/>Broker name.|
|`--limit`|<b>`int`</b><br/>The maximum number of items to list.|
|`--since`|<b>`timestamp`</b><br/>Show logs since this time in HH:MM:SS format or RFC-3339, or duration since now. Examples: '15:04:05', '2006-01-02T15:04:05Z', '2h', '3h30m ago'|
|`--until`|<b>`timestamp`</b><br/>Show logs until this time in HH:MM:SS format or RFC-3339, or duration since now. Examples: '15:04:05', '2006-01-02T15:04:05Z', '2h', '3h30m ago'|
|`-f`,`--follow`|Output logs as they arrive|
|`--levels`|<b>`value[,value]`</b><br/>Show logs with these levels (comma-separated)|
|`--filter`|<b>`string`</b><br/>Use this filter|
|`--max-response-size`|<b>`byteSize`</b><br/>Specifies the maximum response size in bytes. You can also use M and T suffixes to specify MiB or TiB respectively. Default is 3.5 MiB.|

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
