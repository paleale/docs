---
title: Resource relationships in {{ k8s }}
description: The main entity {{ k8s }} operates is a {{ k8s }} cluster. {{ k8s }} clusters consist of a master and one or more node groups. The master is responsible for managing the {{ k8s }} cluster. Containerized user applications run on nodes.
---

# Resource relationships in {{ managed-k8s-name }}


[{{ k8s }}](https://kubernetes.io/) is a containerized application management system. {{ k8s }} provides tools for working with clusters to automate deployment, scaling, and management of applications in containers.

The main entity {{ k8s }} operates is a _{{ k8s }}_ cluster.

## {{ k8s }} cluster {#kubernetes-cluster}

{{ k8s }} clusters consist of a master and one or more node groups. The master is responsible for managing the {{ k8s }} cluster. Containerized user applications run on nodes.

{{ k8s }} fully manages the master and monitors the state and health of node groups. Users can manage nodes directly and configure {{ k8s }} clusters using the {{ yandex-cloud }} management console and the {{ managed-k8s-name }} CLI and API.

{% include [Install kubectl](../../_includes/managed-kubernetes/note-node-group-internet-access.md) %}

{{ k8s }} clusters in the {{ yandex-cloud }} infrastructure use the following resources:

Resource | Amount | Comment
--- | --- | ---
Subnet | 2 | {{ k8s }} reserves IP address ranges to be used for pods and services.
Public IP | N | N includes:<br>* **One** public IP address for the NAT instance.<br>* A public IP address for **each** node in the group if you use one-to-one NAT.

### Cluster metrics {#cluster-labels}

To break {{ k8s }} into logical groups, use [cloud labels](../../resource-manager/concepts/labels.md).

Cloud labels for {{ k8s }} clusters are subject to the following rules:

  {% include [cloud-labels-restrictions-cluster](../../_includes/managed-kubernetes/cloud-labels-restrictions-cluster.md) %}

Learn more about managing cloud labels in [Updating a cluster](../operations/kubernetes-cluster/kubernetes-cluster-update.md#manage-label).

{% note info %}

You cannot assign [{{ k8s }} labels]({{ k8s-docs }}/concepts/overview/working-with-objects/labels/) to a cluster.

{% endnote %}

## Master {#master}

A _master_ is a component that manages a {{ k8s }} cluster.

A master runs {{ k8s }} control processes including the {{ k8s }} API server, scheduler, and main resource controllers. Master lifecycle is managed by {{ k8s }} when creating or deleting a {{ k8s }} cluster. A master is responsible for the global solutions executed on all {{ k8s }} cluster nodes. These include scheduling workloads (such as containerized applications), managing the lifecycle of workloads, and scaling.

There are several types of masters differing by number of master hosts and by their [availability zone](../../overview/concepts/geo-scope.md) placement:
* _Basic_: Contains one master host in a single availability zone. This type of master is cheaper but not fault-tolerant. Its former name is _zonal_.

  {% note warning %}

  {% include [base-zonal-pricing](../../_includes/managed-kubernetes/base-zonal-pricing.md) %}

  {% endnote %}

* `Highly available`: Contains three master hosts, which you can place as follows:
  * In one availability zone and one subnet. This is a good choice of master if you want to ensure high availability of the cluster and reduce network latency within it.
  * In three different availability zones. This master ensures the greatest [fault tolerance](../../architecture/fault-tolerance.md): if one zone becomes unavailable, the master will remain operational.

  The internal IP address of the highly available master is available only within a single {{ vpc-full-name }} cloud network.

  Its former name is _regional_.

  {% note warning %}

  {% include [ha-regional-pricing](../../_includes/managed-kubernetes/ha-regional-pricing.md) %}

  {% endnote %}

For more information about master settings, see [{#T}](../operations/kubernetes-cluster/kubernetes-cluster-create.md).

### Master computing resources {#master-resources}

{% include [master-default-config](../../_includes/managed-kubernetes/master-default-config.md) %}

When [creating](../operations/kubernetes-cluster/kubernetes-cluster-create.md) or [updating](../operations/kubernetes-cluster/kubernetes-cluster-update.md#manage-resources) a cluster, you can select a master configuration suitable for your tasks.

{% include [master-config-preview-note](../../_includes/managed-kubernetes/master-config-preview-note.md) %}

At the Preview stage, you can select a master configuration in the [management console]({{ link-console-main }}).

The following master configurations are available for Intel Cascade Lake with a guaranteed vCPU share of 100%:

* **Standard**: Standard hosts with 4:1 RAM to vCPU ratio: {#master-standard}

  Number of vCPUs | RAM
  --- | ---
  2 | 8
  4 | 16
  8 | 32
  16 | 64
  32 | 128
  64 | 256
  80 | 320

* **CPU-optimized**: Hosts with decreased RAM to vCPU ratio of 2:1: {#master-cpu-optimized}

  Number of vCPUs | RAM
  --- | ---
  4 | 8
  8 | 16
  16 | 32
  32 | 64

* **Memory optimized**: Hosts with increased RAM to vCPU ratio of 8:1: {#master-memory-optimized}

  Number of vCPUs | RAM
  --- | ---
  2 | 16
  4 | 32
  8 | 64
  16 | 128
  32 | 256

You can update the master configuration without stopping the {{ managed-k8s-name }} cluster.

You can also prohibit master configuration updates.

## Node group {#node-group}

A _node group_ is a group of VMs in a {{ k8s }} cluster that have the same configuration and run the user's containers.

{% include [node-vm-explained-short](../../_includes/managed-kubernetes/node-vm-explained-short.md) %}

{% include [node-vm-manipulation-warning](../../_includes/managed-kubernetes/node-vm-manipulation-warning.md) %}

### Configuration {#config}


When creating a group of nodes, you can configure the following VM parameters:
* VM type.
* Type and number of cores (vCPU).
* Amount of memory (RAM) and disk space.
* Kernel parameters.
  * _Safe_ kernel parameters are isolated between pods.
  * _Unsafe_ parameters affect the operation of the pods and the node as a whole. In {{ managed-k8s-name }}, you cannot change unsafe kernel parameters unless their names have been explicitly specified when [creating a node group](../operations/node-group/node-group-create.md).

  {% note info %}

  You should only specify kernel parameters that belong to [namespaces](#namespace), e.g., `net.ipv4.ping_group_range`. Parameters that do not belong to namespaces, e.g., `vm.max_map_count`, should be resolved directly in the OS or using a DaemonSet with containers in privileged mode after creating a [{{ managed-k8s-name }} node group](#node-group).

  {% endnote %}

  For more information about kernel parameters, see the [{{ k8s }} documentation](https://kubernetes.io/docs/tasks/administer-cluster/sysctl-cluster/).

You can create groups with different configurations in a {{ k8s }} cluster and place them in different availability zones.

For {{ managed-k8s-name }}, only [containerd](https://containerd.io/) is available as a container runtime environment.

### Connecting to group nodes {#node-connect-ssh}

You can connect to nodes in a group in the following ways:
* Via an SSH client using a standard SSH key pair, see [{#T}](../operations/node-connect-ssh.md).
* Via an SSH client and the YC CLI using {{ oslogin }}, see [{#T}](../operations/node-connect-oslogin.md).

### Taints and tolerations policies {#taints-tolerations}

_Taints_ are special policies assigned to nodes in the group. Using taints, you can ensure that certain pods are not scheduled onto inappropriate nodes. For example, you can allow the rendering pods to run only on [nodes with GPU](node-group/node-group-gpu.md).

Benefits of taints include:
* The policies persist when a node is restarted or replaced with a new one.
* When adding nodes to a group, the policies are assigned to the node automatically.
* The policies are automatically assigned to new nodes when [scaling a node group](autoscale.md).

You can place a taint on a node group when [creating](../operations/node-group/node-group-create.md) or [changing the group](../operations/node-group/node-group-update.md#assign-taints). If you place a taint on a previously created node group or remove a taint from it, such group will be recreated. First, all nodes in the group are deleted, then nodes with the new configuration are added to the group.

Each taint has three parts:

```text
<key> = <value>:<effect>
```

List of available taint effects:
* `NO_SCHEDULE`: Prohibit running new pods on the group nodes (it does not affect the running ones).
* `PREFER_NO_SCHEDULE`: Avoid running pods on the group nodes if there are resources available for this purpose in other groups.
* `NO_EXECUTE`: Stop pods on the group's nodes, evict them to other groups, and prohibit running new pods.

_Tolerations_: Exceptions from taint policies. With tolerations, you can allow particular pods to run on nodes even if the node group's taint prohibits this.

There are two types of tolerations:

  * `Equal` triggers if the key, value, and effect of the taint match those of the toleration. Used by default.

  * `Exists` triggers if the key and effect of the taint match those of the toleration. The key value is ignored.

  For example, if the `key1=value1:NoSchedule` taint is set for the group's nodes, you can use tolerations to put pods on a node like this:

  ```yaml
  apiVersion: v1
  kind: Pod
  ...
  spec:
    ...
    tolerations:
    - key: "key1"
      operator: "Equal"
      value: "value1"
      effect: "NoSchedule"
  ```
  Or like this:

  ```yaml
  apiVersion: v1
  kind: Pod
  ...
  spec:
    ...
    tolerations:
    - key: "key1"
      operator: "Exists"
      effect: "NoSchedule"
  ```

{% note info %}

System pods are automatically assigned tolerations so they can run on any available node.

{% endnote %}

For more information about taints and tolerations, see the [{{ k8s }} documentation](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/).

### Node labels {#node-labels}

_Node labels_ is a mechanism for grouping nodes in {{ managed-k8s-name }}. There are two types of node labels:

* [Cloud labels](../../resource-manager/concepts/labels.md) are used for logical separation and labeling of resources. For example, you can use cloud labels to [track how much you spend](../../billing/operations/get-folder-report.md#format) on different node groups. They are designated as `template-labels` in the CLI and as `labels` in {{ TF }}.

  Cloud labels for nodes are subject to the following rules:

    {% include [cloud-labels-restrictions-nodes](../../_includes/managed-kubernetes/cloud-labels-restrictions-nodes.md) %}

  Learn more about managing cloud labels in [Updating a node group](../operations/node-group/node-group-update.md#manage-label).

* [{{ k8s }} labels]({{ k8s-docs }}/concepts/overview/working-with-objects/labels/) are used to group {{ k8s }} objects and [distribute pods across cluster nodes](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes). They are designated as `node-labels` in the CLI and as `node_labels` in {{ TF }}.

  When setting {{ k8s }} labels, specify the node characteristics to group objects by. You can find sample {{ k8s }} labels in the [{{ k8s }} documentation]({{ k8s-docs }}/concepts/overview/working-with-objects/labels/#motivation).

  You can define a set of `key: value` {{ k8s }} labels for every object. All of its keys must be unique.

  {% include [k8s-labels-restrictions-nodes](../../_includes/managed-kubernetes/k8s-labels-restrictions-nodes.md) %}

You can use the [{{ managed-k8s-name }} API](../managed-kubernetes/api-ref/index.md) and [{{ k8s }} API]({{ k8s-docs }}/concepts/overview/kubernetes-api) for {{ k8s }} label management. Their features:

  * {{ k8s }} labels added via the {{ k8s }} API may be lost because, when [updating or modifying a node group](../operations/node-group/node-group-update.md), some nodes are recreated with different names and some of the old ones are deleted.
  * If {{ k8s }} labels are created via the {{ managed-k8s-name }} API, you cannot delete them using the {{ k8s }} API. Otherwise, the labels will be restored once they are deleted.

  {% note warning %}

  To make sure no labels are lost, use the {{ managed-k8s-name }} API.

  {% endnote %}

  For more information about adding and deleting {{ k8s }} labels, see [{#T}](../operations/node-group/node-label-management.md). Adding or deleting a label will not result in the node group recreation.

You can use both types of labels at the same time, e.g., when [creating a node group](../operations/node-group/node-group-create.md) in the CLI or {{ TF }}.

## Pod {#pod}

A _pod_ is a request to run one or more containers on a group node. In a {{ k8s }} cluster, each pod has a unique IP address so that applications do not conflict when using ports.

Containers are described in pods via JSON or YAML objects.

### IP masquerade for pods {#pod-ip-masquerade}

If a pod needs access to resources outside the cluster, its IP address will be replaced by the IP address of the node the pod is running on. For this, the cluster uses [IP masquerading](https://kubernetes.io/docs/tasks/administer-cluster/ip-masq-agent/).

By default, masquerading is enabled in the direction of the entire IP range except the directions of pod CIDRs and [link-local address](https://en.wikipedia.org/wiki/Link-local_address) CIDRs.

To implement IP masquerading, the `ip-masq-agent` pod is deployed on each cluster node. The settings for this pod are stored in a ConfigMap object called `ip-masq-agent`. If you need to disable pod IP masquerading in a particular direction, e.g., to access the pods over a VPN or [{{ interconnect-full-name }}](../../interconnect/index.yaml), specify the IP ranges you need in the `data.config.nonMasqueradeCIDRs` parameter:

```yaml
...
data:
  config: |+
    nonMasqueradeCIDRs:
      - <CIDRs_of_IP_addresses_not_requiring_masquerading>
...
```

To view how the IP masquerading rules are configured in `iptables` on a specific node, [connect to the node over SSH](../operations/node-connect-ssh.md) and run this command:

```bash
sudo iptables -t nat -L IP-MASQ -v -n
```

For more information, see the [ip-masq-agent page on GitHub](https://github.com/kubernetes-sigs/ip-masq-agent).

## Service {#service}

A [_service_](service.md) is an abstraction that provides network load balancing functions. Traffic rules are configured for a group of pods united by a set of labels.

By default, a service is only available within a specific {{ k8s }} cluster, but it can be public and receive [requests from outside](../operations/create-load-balancer.md#lb-create) the {{ k8s }} cluster.

## Namespace {#namespace}

A _namespace_ is an abstraction that logically isolates {{ k8s }} cluster resources and distributes [quotas]({{ link-console-quotas }}) to them. This is useful for isolating resources of different teams and projects in a single {{ k8s }} cluster.

## Service accounts {#service-accounts}

{{ managed-k8s-name }} clusters use two types of service accounts:
* **Cloud service accounts**

  These accounts exist at the level of an individual folder in the cloud and can be used both by {{ managed-k8s-name }} and other services.

  For more information, see [{#T}](../security/index.md) and [{#T}](../../iam/concepts/users/service-accounts.md).
* **{{ k8s }} service accounts** 

  These accounts exist and run only at a level of an individual {{ managed-k8s-name }} cluster. {{ k8s }} uses them for:
  * To authenticate cluster API calls from applications deployed in the cluster.
  * To configure access for these applications.

  When deploying a {{ managed-k8s-name }} cluster, a set of {{ k8s }} service accounts is automatically created in the `kube-system` namespace.

  For authentication within the {{ k8s }} cluster hosting the service account, create a token for this account manually.

  For more information, see [{#T}](../operations/connect/create-static-conf.md) and the [{{ k8s }} documentation](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/).

{% note warning %}

Do not confuse [cloud service accounts](../security/index.md#sa-annotation) with {{ k8s }} service accounts.

In the service documentation, _service account_ refers to a regular cloud service account unless otherwise specified.

{% endnote %}

## {{ managed-k8s-name }} cluster statistics {#metrics}

{% include [metrics-resources-list](../../_includes/managed-kubernetes/metrics-resources-list.md) %}

{% include [metrics-k8s-tools](../../_includes/managed-kubernetes/metrics-k8s-tools.md) %}

You can find metrics description in [{#T}](../../managed-kubernetes/metrics.md).

## Use cases {#examples}

* [{#T}](../tutorials/k8s-cluster-with-no-internet.md)
* [{#T}](../tutorials/kubernetes-backup.md)
* [{#T}](../tutorials/prometheus-grafana-monitoring.md)
* [{#T}](../tutorials/metrics-server.md)
* [{#T}](../tutorials/driverless-gpu.md)
