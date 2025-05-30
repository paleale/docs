# Deploying GlusterFS in high availability mode


[GlusterFS](https://en.wikipedia.org/wiki/GlusterFS) is a parallel, distributed, and scalable file system. With horizontal scaling, the system provides the cloud with an aggregate bandwidth in the tens of GB/s and hundreds of thousands of [IOPS](https://en.wikipedia.org/wiki/IOPS).

Use this tutorial to create an infrastructure made up of three segments sharing a common GlusterFS file system. Placing storage [disks](../../compute/concepts/disk.md) in three different [availability zones](../../overview/concepts/geo-scope.md) will ensure the high availability and fault tolerance of your file system.

To configure a high availability file system:

1. [Get your cloud ready](#prepare-cloud).
1. [Configure the CLI profile](#setup-profile).
1. [Set up an environment for deploying the resources](#setup-environment).
1. [Deploy your resources](#deploy-resources).
1. [Install and configure GlusterFS](#install-glusterfs).
1. [Test the solution’s availability and fault tolerance](#test-glusterfs).

If you no longer need the resources you created, [delete them](#clear-out).
 
## Get your cloud ready {#prepare-cloud}

{% include [before-you-begin](../../_tutorials/_tutorials_includes/before-you-begin.md) %}

### Required paid resources {#paid-resources}

The infrastructure support costs include:

* Fee for continuously running VMs and disks (see [{{ compute-full-name }} pricing](../../compute/pricing.md)).
* Fee for using public IP addresses and outbound traffic (see [{{ vpc-full-name }} pricing](../../vpc/pricing.md)).

## Configure the CLI profile {#setup-profile}

1. If you do not have the {{ yandex-cloud }} CLI yet, [install](../../cli/quickstart.md) it and get authenticated according to instructions provided.
1. Create a service account:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder where you want to create a service account.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_iam }}**.
      1. Click **{{ ui-key.yacloud.iam.folder.service-accounts.button_add }}**.
      1. Specify the service account name, e.g., `sa-glusterfs`.
      1. Click **{{ ui-key.yacloud.iam.folder.service-account.popup-robot_button_add }}**.

   - CLI {#cli}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      Run the command below to create a service account, specifying `sa-glusterfs` as its name:

      ```bash
      yc iam service-account create --name sa-glusterfs
      ```

      Where `name` is the service account name.

      Result:
      ```text
      id: ajehr0to1g8b********
      folder_id: b1gv87ssvu49********
      created_at: "2023-06-20T09:03:11.665153755Z"
      name: sa-glusterfs
      ```

   - API {#api}

      To create a service account, use the [ServiceAccountService/Create](../../iam/api-ref/grpc/ServiceAccount/create.md) gRPC API call or the [create](../../iam/api-ref/ServiceAccount/create.md) REST API method for the `ServiceAccount` resource.

   {% endlist %}

1. Assign the administrator [role](../../iam/concepts/access-control/roles.md) for the folder to the service account:

   {% list tabs group=instructions %}

   - Management console {#console}

      1. On the management console [home page]({{ link-console-main }}), select a folder.
      1. Go to the **{{ ui-key.yacloud.common.resource-acl.label_access-bindings }}** tab.
      1. Find the `sa-glusterfs` account in the list and click ![image](../../_assets/options.svg).
      1. Click **{{ ui-key.yacloud.common.resource-acl.button_assign-binding }}**.
      1. Click **{{ ui-key.yacloud_components.acl.button.add-role }}** in the dialog that opens and select the `admin` role.

   - CLI {#cli}

      Run this command:
      ```
      yc resource-manager folder add-access-binding <folder_ID> \
         --role admin \
         --subject serviceAccount:<service_account_ID>
      ```

   - API {#api}

      To assign a role for a folder to a service account, use the [setAccessBindings](../../iam/api-ref/ServiceAccount/setAccessBindings.md) REST API method for the [ServiceAccount](../../iam/api-ref/ServiceAccount/index.md) resource or the [ServiceAccountService/SetAccessBindings](../../iam/api-ref/grpc/ServiceAccount/setAccessBindings.md) gRPC API call.

   {% endlist %}

1. Set up the CLI profile to run operations on behalf of the service account:

   {% list tabs group=instructions %}

   - CLI {#cli}

      1. Create an [authorized key](../../iam/concepts/authorization/key.md) for the service account and save it to the file:
         ```
         yc iam key create \
         --service-account-id <service_account_ID> \
         --folder-id <ID_of_folder_with_service_account> \
         --output key.json
         ```
         Where:
         * `service-account-id`: Service account ID.
         * `folder-id`: Service account folder ID.
         * `output`: Authorized key file name.

         Result:
         ```
         id: aje8nn871qo4********
         service_account_id: ajehr0to1g8b********
         created_at: "2023-06-20T09:16:43.479156798Z"
         key_algorithm: RSA_2048
         ```

      1. Create a CLI profile to run operations on behalf of the service account:
         ```
         yc config profile create sa-glusterfs
         ```

         Result:
         ```
         Profile 'sa-glusterfs' created and activated
         ```

      1. Configure the profile:
         ```
         yc config set service-account-key key.json
         yc config set cloud-id <cloud_ID>
         yc config set folder-id <folder_ID>
         ```

         Where:
         * `service-account-key`: Authorized key file name.
         * `cloud-id`: [Cloud ID](../../resource-manager/operations/cloud/get-id.md).
         * `folder-id`: [Folder ID](../../resource-manager/operations/folder/get-id.md).

      1. Export your credentials to environment variables:
         ```
         export YC_TOKEN=$(yc iam create-token)
         export YC_CLOUD_ID=$(yc config get cloud-id)
         export YC_FOLDER_ID=$(yc config get folder-id)
         ```

    {% endlist %}


## Set up your resource environment {#setup-environment}

1. Create an SSH key pair:
   ```bash
   ssh-keygen -t ed25519
   ```
   We recommend using the default key file name.
1. [Install {{ TF }}](../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).
1. Clone the `yandex-cloud-examples/yc-distributed-ha-storage-with-glusterfs` GitHub repository and go to the `yc-distributed-ha-storage-with-glusterfs` folder:
    ```
    git clone https://github.com/yandex-cloud-examples/yc-distributed-ha-storage-with-glusterfs.git
    cd ./yc-distributed-ha-storage-with-glusterfs
    ```
1. Edit the `variables.tf` file, specifying the parameters of the resources you are deploying:

   {% note warning %}

   The values set in the file result in deploying a resource-intensive infrastructure.
   To deploy the resources within your available quotas, use the values below or adjust the values to your specific needs.

   {% endnote %}

   1. In `disk_size`, change `default` to `30`.
   1. In `client_cpu_count`, change `default` to `2`.
   1. In `storage_cpu_count`, change `default` to `2`.
   1. If you used a non-default name when creating the SSH key pair, change `default` to `<public_SSH_key_path>` under `local_pubkey_path`.

## Deploy your resources {#deploy-resources}

   1. Initialize {{ TF }}:
      ```bash
      terraform init
      ```
   1. Check the {{ TF }} file configuration:
      ```bash
      terraform validate
      ```
   1. Preview the list of new cloud resources:
      ```bash
      terraform plan
      ```
   1. Create the resources:
      ```bash
      terraform apply -auto-approve
      ```
   1. Wait until you are notified it has been completed:
      ```bash
      Outputs:

      connect_line = "ssh storage@158.160.108.137"
      public_ip = "158.160.108.137"
      ```

   This will create three VMs for hosting client code (`client01`, `client02`, and `client03`) in the folder, as well as three VMs for distributed data storage (`gluster01`, `gluster02`, and `gluster03`) linked to the client VMs and placed in three different availability zones.

## Install and configure GlusterFS {#install-glusterfs}

   1. Connect to the `client01` VM using the command from the process completion output:
      ```bash
      ssh storage@158.160.108.137
      ```
   1. Switch to the `root` mode:
      ```bash
      sudo -i
      ```
   1. Install [ClusterShell](https://clustershell.readthedocs.io/en/latest/intro.html):
      ```bash
      dnf install epel-release -y
      dnf install clustershell -y
      echo 'ssh_options: -oStrictHostKeyChecking=no' >> /etc/clustershell/clush.conf
      ```
   1. Create the configuration files:
      ```bash
      cat > /etc/clustershell/groups.conf <<EOF
      [Main]
      default: cluster
      confdir: /etc/clustershell/groups.conf.d $CFGDIR/groups.conf.d
      autodir: /etc/clustershell/groups.d $CFGDIR/groups.d
      EOF

      cat > /etc/clustershell/groups.d/cluster.yaml <<EOF
      cluster:
          all: '@clients,@gluster'
          clients: 'client[01-03]'
          gluster: 'gluster[01-03]'
      EOF 
      ```
   1. Install GlusterFS:
      ```bash
      clush -w @all hostname # check and auto add fingerprints
      clush -w @all dnf install centos-release-gluster -y
      clush -w @all dnf --enablerepo=powertools install glusterfs-server -y
      clush -w @gluster mkfs.xfs -f -i size=512 /dev/vdb
      clush -w @gluster mkdir -p /bricks/brick1
      clush -w @gluster "echo '/dev/vdb /bricks/brick1 xfs defaults 1 2' >> /etc/fstab"
      clush -w @gluster "mount -a && mount"
      ```
   1. Restart GlusterFS:
      ```bash
      clush -w @gluster systemctl enable glusterd
      clush -w @gluster systemctl restart glusterd
      ```
   1. Check whether `gluster02` and `gluster03` are available:
      ```bash
      clush -w gluster01 gluster peer probe gluster02
      clush -w gluster01 gluster peer probe gluster03
      ```
   1. Create a `vol0` folder in each data storage VM and configure availability and fault tolerance by connecting to the `regional-volume` shared folder:
      ```bash
      clush -w @gluster mkdir -p /bricks/brick1/vol0
      clush -w gluster01 gluster volume create regional-volume disperse 3 redundancy 1 gluster01:/bricks/brick1/vol0 gluster02:/bricks/brick1/vol0 gluster03:/bricks/brick1/vol0
      ```

   1. Make use of additional performance settings:
      ```bash
      clush -w gluster01 gluster volume set regional-volume client.event-threads 8
      clush -w gluster01 gluster volume set regional-volume server.event-threads 8
      clush -w gluster01 gluster volume set regional-volume cluster.shd-max-threads 8
      clush -w gluster01 gluster volume set regional-volume performance.read-ahead-page-count 16
      clush -w gluster01 gluster volume set regional-volume performance.client-io-threads on
      clush -w gluster01 gluster volume set regional-volume performance.quick-read off 
      clush -w gluster01 gluster volume set regional-volume performance.parallel-readdir on 
      clush -w gluster01 gluster volume set regional-volume performance.io-thread-count 32
      clush -w gluster01 gluster volume set regional-volume performance.cache-size 1GB
      clush -w gluster01 gluster volume set regional-volume server.allow-insecure on
      ```
   1. Mount the `regional-volume` shared folder on the client VMs:
      ```bash
      clush -w gluster01 gluster volume start regional-volume
      clush -w @clients mount -t glusterfs gluster01:/regional-volume /mnt/
      ```

## Test the availability and fault tolerance of the solution {#test-glusterfs}

   1. Check the status of the `regional-volume` shared folder:
      ```bash
      clush -w gluster01 gluster volume status
      ```
      Result:

      ```bash
      gluster01: Status of volume: regional-volume
      gluster01: Gluster process                             TCP Port  RDMA Port  Online  Pid
      gluster01: ------------------------------------------------------------------------------
      gluster01: Brick gluster01:/bricks/brick1/vol0         54660     0          Y       1374
      gluster01: Brick gluster02:/bricks/brick1/vol0         58127     0          Y       7716
      gluster01: Brick gluster03:/bricks/brick1/vol0         53346     0          Y       7733
      gluster01: Self-heal Daemon on localhost               N/A       N/A        Y       1396
      gluster01: Self-heal Daemon on gluster02               N/A       N/A        Y       7738
      gluster01: Self-heal Daemon on gluster03               N/A       N/A        Y       7755
      gluster01:
      gluster01: Task Status of Volume regional-volume
      gluster01: ------------------------------------------------------------------------------
      gluster01: There are no active volume tasks
      gluster01:
      ```

   1. Create a text file:
      ```bash
      cat > /mnt/test.txt <<EOF
      Hello, GlusterFS!
      EOF
      ```
   
   1. Make sure the file is available on all three client VMs:
      ```bash
      clush -w @clients sha256sum /mnt/test.txt
      ```
      Result:
      ```bash
      client01: 5fd9c031531c39f2568a8af5512803fad053baf3fe9eef2a03ed2a6f0a884c85  /mnt/test.txt
      client02: 5fd9c031531c39f2568a8af5512803fad053baf3fe9eef2a03ed2a6f0a884c85  /mnt/test.txt
      client03: 5fd9c031531c39f2568a8af5512803fad053baf3fe9eef2a03ed2a6f0a884c85  /mnt/test.txt
      ```
   1. Shut down one of the storage VMs, e.g., `gluster02`:

      {% list tabs group=instructions %}

      - Management console {#console}

        1. In the [management console]({{ link-console-main }}), select the folder this VM belongs to.
        1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
        1. Select the `gluster02` VM from the list, click ![image](../../_assets/options.svg), and select **{{ ui-key.yacloud.common.stop }}**.
        1. In the window that opens, click **{{ ui-key.yacloud.compute.instances.popup-confirm_button_stop }}**.

      - CLI {#cli}

        1. See the description of the CLI command for stopping a VM:

           ```bash
           yc compute instance stop --help
           ```
        1. Stop the VM:

           ```bash
           yc compute instance stop gluster02
           ```

      - API {#api}

        Use the [stop](../../compute/api-ref/Instance/stop.md) REST API method for the [Instance](../../compute/api-ref/Instance/) resource or the [InstanceService/Stop](../../compute/api-ref/grpc/Instance/stop.md) gRPC API call.

      {% endlist %}

   1. Make sure the VM is shut down:
      ```bash
      clush -w gluster01  gluster volume status
      ```
      Result:

      ```bash
      gluster01: Status of volume: regional-volume
      gluster01: Gluster process                             TCP Port  RDMA Port  Online  Pid
      gluster01: ------------------------------------------------------------------------------
      gluster01: Brick gluster01:/bricks/brick1/vol0         54660     0          Y       1374
      gluster01: Brick gluster03:/bricks/brick1/vol0         53346     0          Y       7733
      gluster01: Self-heal Daemon on localhost               N/A       N/A        Y       1396
      gluster01: Self-heal Daemon on gluster03               N/A       N/A        Y       7755
      gluster01:
      gluster01: Task Status of Volume regional-volume
      gluster01: ------------------------------------------------------------------------------
      gluster01: There are no active volume tasks
      gluster01:
      ```

   1. Make sure the file is still available on all three client VMs:
      ```bash
      clush -w @clients sha256sum /mnt/test.txt
      ```
      Result:
      ```bash
      client01: 5fd9c031531c39f2568a8af5512803fad053baf3fe9eef2a03ed2a6f0a884c85  /mnt/test.txt
      client02: 5fd9c031531c39f2568a8af5512803fad053baf3fe9eef2a03ed2a6f0a884c85  /mnt/test.txt
      client03: 5fd9c031531c39f2568a8af5512803fad053baf3fe9eef2a03ed2a6f0a884c85  /mnt/test.txt
      ```

## How to delete the resources you created {#clear-out}

To stop paying for the resources created, delete them:
   ```bash
   terraform destroy -auto-approve
   ```
