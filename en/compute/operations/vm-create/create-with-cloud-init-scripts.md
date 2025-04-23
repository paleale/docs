---
title: How to create a VM with a custom script
description: This guide will show you how to use `cloud-init` scripts to install additional software and customize your VM while creating it.
---

# Creating a VM with a custom configuration script

You can create a VM with a preset software configuration by using the `user-data` key in the VM [metadata](../../concepts/vm-metadata.md).

The [cloud-init](https://cloudinit.readthedocs.io/en/latest/) agent running on the VM will process the configuration you set in the `user-data` key. `Cloud-init` supports different metadata transmission formats, e.g., [cloud-config](https://cloudinit.readthedocs.io/en/latest/reference/examples.html).

## Creating a VM with a custom configuration script {#create-vm-with-user-script}

{% note warning %}

In the `user-data` configuration, you must always set the user login and SSH key, even if you have already specified them under **{{ ui-key.yacloud.compute.instances.create.section_access }}** in the management console.

{% endnote %}

To create a VM with a custom configuration script:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) where you want to create your VM.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}** from the list of services.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/server.svg) **{{ ui-key.yacloud.compute.switch_instances }}**.
  1. Click **{{ ui-key.yacloud.compute.instances.button_create }}**.
  1. [Set](create-linux-vm.md) the required VM parameters.
  1. Expand the **{{ ui-key.yacloud.common.metadata }}** section and specify:

      * **{{ ui-key.yacloud_billing.component.key-values-input.label_key }}**: `user-data`.
      * **{{ ui-key.yacloud_billing.component.key-values-input.label_value }}**: `cloud-config` configuration in YAML format. See configuration examples for `user-data` under [Examples](#examples).

  1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

- CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  Run this command:

  ```bash
  yc compute instance create \
    --name my-sample-instance \
    --zone {{ region-id}}-a \
    --network-interface subnet-name=<subnet_name>,nat-ip-version=ipv4,security-group-ids=<security_group_ID> \
    --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-2204-lts,kms-key-id=<key_ID> \
    --metadata-from-file user-data="<path_to_configuration_file>"
  ```

  Where:

  * `--name`: VM name. The naming requirements are as follows:

      {% include [name-format](../../../_includes/name-format.md) %}

      {% include [name-fqdn](../../../_includes/compute/name-fqdn.md) %}

  * `--zone`: [Availability zone](../../../overview/concepts/geo-scope.md) matching the selected subnet.
  * `--network-interface`: VM [network interface](../../concepts/network.md) settings:

      * `subnet-name`: Name of the [subnet](../../../vpc/concepts/network.md#subnet) in the [availability zone](../../../overview/concepts/geo-scope.md) specified in the `--zone` parameter.
      * `security-group-ids`: [Security group](../../../vpc/concepts/security-groups.md) ID.

  * `--create-boot-disk`: VM boot disk settings:

      * `image-family`: [Image family](../../concepts/image.md#family), e.g., `ubuntu-2204-lts`. This option allows you to install the latest version of the OS from the specified family.
      * `kms-key-id`: ID of the [{{ kms-short-name }} symmetric key](../../../kms/concepts/key.md) to create an encrypted boot disk. This is an optional parameter.

        {% include [encryption-role](../../../_includes/compute/encryption-role.md) %}
        
        {% include [encryption-disable-warning](../../../_includes/compute/encryption-disable-warning.md) %}

        {% include [encryption-keys-note](../../../_includes/compute/encryption-keys-note.md) %}

  * `--metadata-from-file`: `user-data` key and its value, i.e., path to the `cloud-config` configuration file in YAML format, e.g., `--metadata-from-file user-data="/home/user/metadata.yaml"`.

      See configuration examples for `user-data` under [Examples](#examples).

  {% include [cli-metadata-variables-substitution-notice](../../../_includes/compute/create/cli-metadata-variables-substitution-notice.md) %}

- {{ TF }} {#tf}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  Add the VM configuration script to the `metadata` section of the `yandex_compute_instance` resource in the {{ TF }} configuration file:

  ```hcl
  resource "yandex_compute_instance" "vm-1" {
    name        = "my-sample-instance"
    ...
    metadata    = {
      user-data = "${file("<path_to_configuration_file>")}"
    }
    ...
  }
  ```

  Where:
  * `user-data`: Path to the `cloud-config` configuration file in YAML format, e.g., `user-data = "${file("/home/user/metadata.yaml")}"`.
  
      See configuration examples for `user-data` under [Examples](#examples).

- API {#api}

  To create a VM, use the [create](../../api-ref/Instance/create.md) REST API method for the [Instance](../../api-ref/Instance/) resource and provide the object with the `cloud-config` YAML configuration under `metadata` in the request body. For a multiline configuration, use `\n` as a separator. Here is an example:

  ```json
  {
    "folderId": "b1gvmob95yys********",
    "name": "my-sample-instance",
    "zoneId": "{{ region-id }}-a",
    "platformId": "standard-v3",
    ...
    "metadata": {
      "user-data": "#cloud-config\ndatasource:\n  Ec2:\n    strict_id: false\nssh_pwauth: yes\nusers:\n- name: <username>\n  sudo: 'ALL=(ALL) NOPASSWD:ALL'\n  shell: /bin/bash\n  ssh_authorized_keys:\n  - <public_SSH_key>\nwrite_files:\n  - path: '/usr/local/etc/startup.sh'\n    permissions: '755'\n    content: |\n      #!/bin/bash\n      apt-get update\n      apt-get install -y nginx\n      service nginx start\n      sed -i -- 's/ nginx/ Yandex Cloud - ${HOSTNAME}/' /var/www/html/index.nginx-debian.html\n    defer: true\nruncmd:\n  - ['/usr/local/etc/startup.sh']"
    },
    ...
  }
  ```

  See configuration examples for `user-data` under [Examples](#examples).

{% endlist %}

For more information on how to create a VM, see [{#T}](./create-linux-vm.md).

To make sure the configuration scripts ran successfully, [get the serial port output](../vm-info/get-serial-port-output.md) of your VM.

## Examples {#examples}

{% list tabs %}

- Nginx

  To install and run an [Nginx](https://nginx.org/) web server on the new VM, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  write_files:
    - path: "/usr/local/etc/startup.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        apt-get update
        apt-get install -y nginx
        service nginx start
        sed -i -- "s/ nginx/ Yandex Cloud - ${HOSTNAME}/" /var/www/html/index.nginx-debian.html
      defer: true
  runcmd:
    - ["/usr/local/etc/startup.sh"]
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

- Docker

  To install [Docker](https://www.docker.com) on the new VM and to upload and run a container, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: "ALL=(ALL) NOPASSWD:ALL"
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  write_files:
    - path: "/usr/local/etc/docker-start.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        # Docker
        echo "Installing Docker"
        sudo apt update -y && sudo apt install docker.io -y
        echo "Grant user access to Docker"
        sudo usermod -aG docker ${USER}
        newgrp docker

      defer: true
    - path: "/usr/local/etc/docker-main.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        # Docker run container
        docker pull hello-world:latest
        docker run hello-world

      defer: true
  runcmd:
    - [su, <username>, -c, "/usr/local/etc/docker-start.sh"]
    - [su, <username>, -c, "/usr/local/etc/docker-main.sh"]
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

- {{ yandex-cloud }} CLI

  To install the [{{ yandex-cloud }} CLI](../../../cli/quickstart.md) on the new VM, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  write_files:
    - path: "/usr/local/etc/yc-install.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        # YC CLI
        echo "Installing Yandex Cloud CLI"
        curl \
          --silent \
          --show-error \
          --location \
          https://{{ s3-storage-host }}/yandexcloud-yc/install.sh | bash
        VM_ID=$(curl --silent http://169.254.169.254/latest/meta-data/instance-id)

        # Save YC params
        echo "Saving YC params to the ~/.bashrc"
        cat << EOF >> $HOME/.bashrc
      defer: true
  runcmd:
    - [su, <username>, -c, "/usr/local/etc/yc-install.sh"]
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

- {{ unified-agent-full-name }}

  {% note info %}

  When creating a VM with [{{ unified-agent-full-name }}](../../../monitoring/concepts/data-collection/unified-agent/index.md), link it to a [service account](../../../iam/concepts/users/service-accounts.md) with the `monitoring.editor` [role](../../../monitoring/security/index.md#monitoring-editor) for the current [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder).

  {% endnote %}

  To install {{ unified-agent-short-name }} on the new VM, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  runcmd:
    - wget -O - https://{{ api-host-monitoring-1 }}/monitoring/v2/unifiedAgent/config/install.sh | bash
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

  To link a service account to a VM when creating it using {{ TF }}, add the following row to the configuration:
  
  ```hcl
  resource "yandex_compute_instance" "my-vm" {
    ...
    service_account_id = "ajehka*************"
  }
  ```

  For {{ unified-agent-short-name }} to send metrics to {{ managed-prometheus-full-name }}, specify the workspace ID in the configuration:
  
  ```hcl
  resource "yandex_compute_instance" "my-vm" {
    ...
    metadata    = {
      monitoring_workspaceid = "mon618clr**************"
    }
  }
  ```

- {{ TF }}

  To install [{{ TF }}](https://www.terraform.io/) on the new VM, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  write_files:
    - path: "/usr/local/etc/tf-install.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        # Install Global Ubuntu things
        sudo apt-get update
        echo 'debconf debconf/frontend select Noninteractive' | sudo debconf-set-selections
        sudo apt-get install -y unzip python3-pip

        # Install Terraform
        echo "Installing Terraform"
        sudo curl \
          --silent \
          --show-error \
          --location \
          https://hashicorp-releases.yandexcloud.net/terraform/1.8.5/terraform_1.8.5_linux_amd64.zip \
          --output /usr/local/etc/terraform.zip
        sudo unzip /usr/local/etc/terraform.zip -d /usr/local/etc/
        sudo install -o root -g root -m 0755 /usr/local/etc/terraform /usr/local/bin/terraform
        sudo rm -rf /usr/local/etc/terraform /usr/local/etc/terraform.zip /usr/local/etc/LICENSE.txt
      defer: true
  runcmd:
    - [su, <username>, -c, "/usr/local/etc/tf-install.sh"]
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

- kubectl

  To install [kubectl](https://kubernetes.io/docs/reference/kubectl/) on the new VM, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  write_files:
    - path: "/usr/local/etc/kubectl-install.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        # Install kubectl
        echo "Installing kubectl"
        sudo curl \
          --silent \
          --show-error \
          --location \
          https://dl.k8s.io/release/v1.3.0/bin/linux/amd64/kubectl \
          --output /usr/local/etc/kubectl
        sudo install -o root -g root -m 0755 /usr/local/etc/kubectl /usr/local/bin/kubectl
        sudo rm -rf /usr/local/etc/kubectl
      defer: true
  runcmd:
    - [su, <username>, -c, "/usr/local/etc/kubectl-install.sh"]
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

- Helm

  To install [Helm](https://helm.sh/) on the new VM, specify the following value for the `user-data` key:

  ```yaml
  #cloud-config
  datasource:
    Ec2:
      strict_id: false
  ssh_pwauth: no
  users:
  - name: <username>
    sudo: 'ALL=(ALL) NOPASSWD:ALL'
    shell: /bin/bash
    ssh_authorized_keys:
    - <public_SSH_key>
  write_files:
    - path: "/usr/local/etc/helm-install.sh"
      permissions: "755"
      content: |
        #!/bin/bash

        # Install Helm
        echo "Installing Helm"
        sudo curl \
          --silent \
          --show-error \
          --location \
          https://get.helm.sh/helm-v3.15.2-linux-amd64.tar.gz \
          --output /usr/local/etc/helm-v3.15.2-linux-amd64.tar.gz
        sudo tar xf /usr/local/etc/helm-v3.15.2-linux-amd64.tar.gz -C /usr/local/etc/
        sudo install -o root -g root -m 0755 /usr/local/etc/linux-amd64/helm /usr/local/bin/helm
        sudo rm -rf /usr/local/etc/helm-v3.15.2-linux-amd64.tar.gz /usr/local/etc/linux-amd64
      defer: true
  runcmd:
    - [su, <username>, -c, "/usr/local/etc/helm-install.sh"]
  ```

  {% include [cli-install](../../../_includes/compute/create/legend-for-creating-user-data-scripts.md) %}

{% endlist %}

#### See also {#see-also}

Other configuration examples for the `user-data` key:

* [Creating an instance group connected to a file storage](../instance-groups/create-with-filesystem.md)
* [Creating an instance group connected to {{ objstorage-full-name }}](../instance-groups/create-with-bucket.md)
* [Recovering VM network interfaces](../../qa/troubleshooting.md#unable-to-connect-to-new-multi-interface-vm)
* [{#T}](../../../tutorials/archive/vm-with-backup-policy/index.md)
* [Installing the agent for collecting {{ unified-agent-short-name }} metrics and logs](../../../monitoring/concepts/data-collection/unified-agent/installation.md#setup)
* [Installing the agent for collecting metrics in {{ prometheus-name }} format](../../../monitoring/operations/prometheus/ingestion/prometheus-agent.md)
