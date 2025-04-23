---
title: Device topics in {{ iot-full-name }}
description: This page describes all device topics available in {{ iot-name }}.
---

# Device topics

Device topics available in the service:
* `$devices/<device_ID>/events`: Topic for sending telemetry data.  
* `$devices/<device_ID>/state`: Permanent topic for sending telemetry data.

   The device can write data to these topics and the registry can read the data from them. Registries subscribed to these topics will know which device sent the data, because the topic contains the device ID.

* `$devices/<device_ID>/commands`: Topic for receiving commands.
* `$devices/<device_ID>/config`: Permanent topic for receiving commands.

   The registry can write data to these topics and the device can read data from them. The registry sends commands for a specific device to these topics.

* `$monitoring/<device_ID>/json`: Topic for receiving monitoring data in JSON format.

   The device automatically writes data to this topic, and other devices and registries can read the data from it. Registries or devices subscribed to this topic will know which device sent the data, because the topic contains a device ID.

{% include [registry-and-device-topic-note.md](../../../_includes/iot-core/registry-and-device-topic-note.md) %}

## Monitoring topic {#monitoring-topic}

The device and registry that are subscribed to the monitoring topic will receive up-to-date monitoring data for the device whose ID is specified in the topic.

* `last_auth_time`: Time of the last authentication on the device.
* `last_auth_ip`: IP address the last authentication was made from.
* `last_pub_activity_time`: Time when the device last sent a message.
* `last_sub_activity_time`: Time of the last subscription to messages.
* `last_online_time`: Time of the last activity of the device.
* `registry_id`: ID of the registry where the device is located.
* `device_id`: Device ID.

Time is specified in [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). New messages are sent to registries or devices that are subscribed to the topic every time the device is accessed over MQTT. The quality of service (QoS) level is [at most once](../index.md#qos). If the message failed to arrive within the expected time period, we recommend waiting about two minutes before reacting.

### Example of monitoring data {#example}

```json
{
	"last_auth_time": "2021-03-24T09:55:21.7570928Z",
	"last_auth_ip": "199.21.99.45",
	"last_pub_activity_time": "2021-03-24T09:55:21.858181573Z",
	"last_online_time": "2021-03-24T09:55:21.858181573Z",
	"registry_id": "areljb44npjoaog*****",
	"device_id": "are3tkujvebfo3s*****"
}
```

## Use cases {#examples}

* [{#T}](../../tutorials/data-recording.md)
* [{#T}](../../tutorials/datalens.md)
* [{#T}](../../tutorials/message-delivery-check.md)
* [{#T}](../../tutorials/monitoring.md)
* [{#T}](../../tutorials/mosquitto/mosquitto-publish.md)