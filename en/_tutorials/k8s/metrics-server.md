# Updating the Metrics Server parameters

[Metrics Server](https://github.com/kubernetes-sigs/metrics-server) is a [{{ managed-k8s-name }}](../../managed-kubernetes/concepts/index.md#kubernetes-cluster) cluster service, which is installed by default. It collects metrics from each {{ managed-k8s-name }} cluster [node](../../managed-kubernetes/concepts/index.md#node-group) using `kubelet` and provides them through the [Metrics API](https://github.com/kubernetes/metrics). [Horizontal Pod Autoscaler and Vertical Pod Autoscaler](../../managed-kubernetes/concepts/autoscale.md) run based on data from these metrics. You can get the metric data using the `kubectl top node` or `kubectl top pod` commands. For more information, see the [Metrics Server documentation](https://github.com/kubernetes-sigs/metrics-server#kubernetes-metrics-server).

A Metrics Server [pod](../../managed-kubernetes/concepts/index.md#pod) has two containers: `metrics-server` and `metrics-server-nanny`, the latter acting as an [addon-resizer](https://github.com/kubernetes/autoscaler/tree/master/addon-resizer#addon-resizer) for `metrics-server`. The `metrics-server-nanny` container is responsible for the automatic allocation of resources to the `metrics-server` container depending on the number of {{ managed-k8s-name }} cluster nodes.

In some cases, the `metrics-server-nanny` component may run incorrectly. For instance, if many pods are created while there are few nodes in the {{ managed-k8s-name }} cluster. If so, the Metrics Server pod will exceed its limits, which may degrade the server performance.

To avoid this, change the parameters of the Metrics Server manually:

1. [View the amount of resources allocated to the Metrics Server pod](#get-resources).
1. [Update the Metrics Server parameters](#update-parameters).
1. [Check the result](#check-result).

To restore the default values of the Metrics Server parameters, [reset them](#reset).

## View the amount of resources allocated to the Metrics Server pod {#get-resources}

1. {% include [configure-sg-manual](../../_includes/managed-kubernetes/security-groups/configure-sg-manual-lvl3.md) %}

    {% include [sg-common-warning](../../_includes/managed-kubernetes/security-groups/sg-common-warning.md) %}

1. {% include [Install and configure kubectl](../../_includes/managed-kubernetes/kubectl-install.md) %}
1. Run this command:

    ```bash
    kubectl get pod <Metrics_Server_pod_name> \
      --namespace=kube-system \
      --output=json | \
      jq '.spec.containers[] | select(.name == "metrics-server") | .resources'
    ```

    The resources are calculated using the following formula:

    ```text
    cpu = baseCPU + cpuPerNode * nodesCount
    memory = baseMemory + memoryPerNode * nodesCount
    ```

    Where:

    * `baseCPU`: Basic [number of CPUs](../../compute/concepts/vm-platforms.md).
    * `cpuPerNode`: Number of CPUs per node.
    * `nodesCount`: Number of {{ managed-k8s-name }} nodes.
    * `baseMemory`: Basic amount of RAM.
    * `memoryPerNode`: Amount of RAM per node.

## Update the Metrics Server parameters {#update-parameters}

1. Open the Metrics Server configuration file:

    ```bash
    kubectl edit configmap metrics-server-config \
      --namespace=kube-system \
      --output=yaml
    ```

1. Add or update the resource parameters under `data.NannyConfiguration`:

    ```yaml
    apiVersion: v1
    data:
      NannyConfiguration: |-
        apiVersion: nannyconfig/v1alpha1
        kind: NannyConfiguration
        baseCPU: <basic_number_of_CPUs>m
        cpuPerNode: <number_of_CPUs_per_node>m
        baseMemory: <basic_amount_of_RAM>Mi
        memoryPerNode: <amount_of_RAM_per_node>Mi
    ...
    ```

    {% cut "Sample configuration file" %}

    ```yaml
    apiVersion: v1
    data:
      NannyConfiguration: |-
        apiVersion: nannyconfig/v1alpha1
        kind: NannyConfiguration
        baseCPU: 48m
        cpuPerNode: 1m
        baseMemory: 104Mi
        memoryPerNode: 3Mi
    kind: ConfigMap
    metadata:
      creationTimestamp: "2022-12-15T06:28:22Z"
      labels:
        addonmanager.kubernetes.io/mode: EnsureExists
      name: metrics-server-config
      namespace: kube-system
      resourceVersion: "303569"
      uid: 931b88ca-21da-4d04-a3c1-da7e********
    ```

    {% endcut %}

1. Restart the Metrics Server. To do this, delete it and wait until the {{ k8s }} controller creates it again:

    ```bash
    kubectl delete deployment metrics-server \
      --namespace=kube-system
    ```

## Check the result {#check-result}

[View](#get-resources) the amount of resources allocated to the Metrics Server pod once again and make sure it has been updated for the new pod.

## Reset the Metrics Server parameters {#reset}

To reset the parameters to their default values, delete the Metrics Server configuration file and its Deployment:

```bash
kubectl delete configmap metrics-server-config \
  --namespace=kube-system && \
kubectl delete deployment metrics-server \
  --namespace=kube-system
```

As a result, a new pod of the Metrics Server is created automatically.
