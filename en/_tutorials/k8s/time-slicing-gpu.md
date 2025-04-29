# Setting up Time-Slicing GPUs


The [Time-Slicing GPUs plugin in {{ k8s }}](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/gpu-sharing.html) is used to alternate workloads that run on a single [GPU](../../compute/concepts/gpus.md) with oversubscription.

To install the Time-Slicing GPUs plugin in {{ managed-k8s-name }}:

1. [Configure Time-Slicing GPUs](#configure-time-slicing).
1. [Test Time-Slicing GPUs](#check-time-slicing).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ managed-k8s-name }} cluster fee: using the master and outgoing traffic (see [{{ managed-k8s-name }} pricing](../../managed-kubernetes/pricing.md)).
* Cluster nodes (VM) fee: using computing resources, operating system, and storage (see [{{ compute-name }} pricing](../../compute/pricing.md)).
* Fee for a public IP address assigned to cluster nodes (see [{{ vpc-name }} pricing](../../vpc/pricing.md#prices-public-ip)).


## Getting started {#before-you-begin}

1. {% include [cli-install](../../_includes/cli-install.md) %}

1. {% include [default-catalogue](../../_includes/default-catalogue.md) %}

1. {% include [configure-sg-manual](../../_includes/managed-kubernetes/security-groups/configure-sg-manual-lvl3.md) %}

    {% include [sg-common-warning](../../_includes/managed-kubernetes/security-groups/sg-common-warning.md) %}

1. [Create a {{ managed-k8s-name }} cluster](../../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-create.md). When creating them, specify the security groups prepared earlier.

1. [Create a {{ managed-k8s-name }} node group](../../managed-kubernetes/operations/node-group/node-group-create.md) with [GPU NVIDIA® Tesla® T4](../../compute/concepts/gpus.md#tesla-t4) and the security groups prepared earlier.

1. {% include [kubectl-install](../../_includes/managed-kubernetes/kubectl-install.md) %}

## Configure Time-Slicing GPUs {#configure-time-slicing}

1. Create a time-slicing configuration:
   1. Prepare the `time-slicing-config.yaml` file with the following content:

      ```yaml
      ---
      kind: Namespace
      apiVersion: v1
      metadata:
        name: gpu-operator

      ---
      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: time-slicing-config
        namespace: gpu-operator
      data:
        a100-80gb: |-
          version: v1
          sharing:
            timeSlicing:
              resources:
              - name: nvidia.com/gpu
                replicas: 5
        tesla-t4: |-
          version: v1
          sharing:
            timeSlicing:
              resources:
              - name: nvidia.com/gpu
                replicas: 5
      ```

   1. Run this command:

      ```bash
      kubectl create -f time-slicing-config.yaml
      ```

      Result:

      ```text
      namespace/gpu-operator created
      configmap/time-slicing-config created
      ```

1. Install the [GPU operator](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/overview.html):

   ```bash
   helm repo add nvidia https://helm.ngc.nvidia.com/nvidia && \
   helm repo update && \
   helm install \
     --namespace gpu-operator \
     --create-namespace \
     --set devicePlugin.config.name=time-slicing-config \
     gpu-operator nvidia/gpu-operator
   ```

1. Apply the time-slicing configuration to the [{{ managed-k8s-name }} cluster](../../managed-kubernetes/concepts/index.md#kubernetes-cluster) or [node group](../../managed-kubernetes/concepts/index.md#node-group):

   {% list tabs %}

   - {{ managed-k8s-name }} cluster

     ```bash
     kubectl patch clusterpolicies.nvidia.com/cluster-policy \
       --namespace gpu-operator \
       --type merge \
       --patch='{"spec": {"devicePlugin": {"config": {"name": "time-slicing-config", "default": "tesla-t4"}}}}'
     ```

   - Node group {{ managed-k8s-name }}

     ```bash
     yc managed-kubernetes node-group add-labels <node_group_ID_or_name> \
       --labels nvidia.com/device-plugin.config=tesla-t4
     ```

     You can get the ID and name of the {{ managed-k8s-name }} node group with a [list of node groups in your cluster](../../managed-kubernetes/operations/node-group/node-group-list.md#list).

   {% endlist %}

## Test Time-Slicing GPUs {#check-time-slicing}

1. Create a test app:
   1. Save the following app creation specification to a YAML file named `nvidia-plugin-test.yml`.

      [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) is the {{ k8s }} API object that manages the replicated application.

      ```yaml
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nvidia-plugin-test
        labels:
          app: nvidia-plugin-test
      spec:
        replicas: 5
        selector:
          matchLabels:
            app: nvidia-plugin-test
        template:
          metadata:
            labels:
              app: nvidia-plugin-test
          spec:
            tolerations:
              - key: nvidia.com/gpu
                operator: Exists
                effect: NoSchedule
            containers:
              - name: dcgmproftester11
                image: nvidia/samples:dcgmproftester-2.0.10-cuda11.0-ubuntu18.04
                command: ["/bin/sh", "-c"]
                args:
                  - while true; do /usr/bin/dcgmproftester11 --no-dcgm-validation -t 1004 -d 300; sleep 30; done
                resources:
                  limits:
                    nvidia.com/gpu: 1
                securityContext:
                  capabilities:
                    add: ["SYS_ADMIN"]
      ```

   1. Run this command:

      ```bash
      kubectl apply -f nvidia-plugin-test.yml
      ```

      Result:

      ```text
      deployment.apps/nvidia-plugin-test created
      ```

1. Make sure all the app's five [{{ managed-k8s-name }} pods](../../managed-kubernetes/concepts/index.md#pod) are `Running`:

   ```bash
   kubectl get pods | grep nvidia-plugin-test
   ```

1. Run the `nvidia-smi` command in the active `nvidia-container-toolkit` {{ managed-k8s-name }} pod:

   ```bash
   kubectl exec <nvidia-container-toolkit_pod_name> \
     --namespace gpu-operator -- nvidia-smi
   ```

   Result:

   ```text
   Defaulted container "nvidia-container-toolkit-ctr" out of: nvidia-container-toolkit-ctr, driver-validation (init)
   Thu Jan 26 09:42:51 2023
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 515.65.01    Driver Version: 515.65.01    CUDA Version: N/A      |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |                               |                      |               MIG M. |
   |===============================+======================+======================|
   |   0  Tesla T4            Off  | 00000000:8B:00.0 Off |                    0 |
   | N/A   72C    P0    70W /  70W |   1579MiB / 15360MiB |    100%      Default |
   |                               |                      |                  N/A |
   +-------------------------------+----------------------+----------------------+

   +-----------------------------------------------------------------------------+
   | Processes:                                                                  |
   |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
   |        ID   ID                                                   Usage      |
   |=============================================================================|
   |    0   N/A  N/A     43108      C   /usr/bin/dcgmproftester11         315MiB |
   |    0   N/A  N/A     43211      C   /usr/bin/dcgmproftester11         315MiB |
   |    0   N/A  N/A     44583      C   /usr/bin/dcgmproftester11         315MiB |
   |    0   N/A  N/A     44589      C   /usr/bin/dcgmproftester11         315MiB |
   |    0   N/A  N/A     44595      C   /usr/bin/dcgmproftester11         315MiB |
   +-----------------------------------------------------------------------------+
   ```

## Delete the resources you created {#clear-out}

Some resources are not free of charge. Delete the resources you no longer need to avoid paying for them:
1. [Delete the {{ managed-k8s-name }} cluster](../../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-delete.md).
1. If you had created any [service accounts](../../iam/concepts/users/service-accounts.md), [delete them](../../iam/operations/sa/delete.md).
