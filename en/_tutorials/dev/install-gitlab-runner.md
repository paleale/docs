# Deploying {{ GLR }} on a {{ compute-full-name }} virtual machine

[{{ GLR }}](https://docs.gitlab.com/runner/) is an open-source application that executes {{ GL }} CI/CD pipeline jobs based on instructions from a special file named `.gitlab-ci.yml`. You can deploy {{ GLR }} either in a [{{ managed-k8s-full-name }}](../../managed-kubernetes/concepts/index.md#kubernetes-cluster) cluster or a {{ compute-name }} virtual machine, which is an easier and cheaper option.

{{ compute-name }} offers two ways to work with {{ GLR }}: You can:

* Create a [VM](../../compute/concepts/vm.md) and install {{ GLR }} on it manually.
* Using the [management console]({{ link-console-main }}), create a runner that will automatically deploy the specified number of VMs ready to run jobs.

To get started with {{ GLR }} using {{ compute-name }}:

1. [Set up your infrastructure](#infra).
1. [Get a {{ GLR }} token](#gitlab-token).
1. [Install the {{ GLR }} agent on the {{ compute-full-name }}](#install) VM or [create a runner using the management console](#create-runner).
1. [Create a test scenario](#example).

If you no longer need the resources you created, [delete them](#clear-out).

## Required paid resources {#paid-resources}

The infrastructure support cost includes:

* Fee for [disks](../../compute/concepts/disk.md) and continuously running VMs (see [{{ compute-full-name }} pricing](../../compute/pricing.md)).
* Fee for using a [public IP address](../../vpc/concepts/address.md#public-addresses) (see [{{ vpc-full-name }} pricing](../../vpc/pricing.md)).

## Set up your infrastructure {#infra}

1. [Create and activate](../../managed-gitlab/operations/instance/instance-create.md) a {{ mgl-name }} instance.
1. [Create a {{ GL }}]({{ gl.docs }}/ee/user/project/) project.

## Get a {{ GLR }} token {#gitlab-token}

* To configure {{ GLR }} throughout the [{{ GL }}](../../managed-gitlab/concepts/index.md#instance) instance ({{ GL }} administrator access required):

  1. Open {{ GL }}.
  1. In the bottom-left corner, click **Admin**. 
  1. In the left-hand menu, select **CI/CD** → **Runners**.
  1. Click **New instance runner** and create a new {{ GLR }}.
  1. Save the value of the `Runner authentication token` parameter.

* To configure {{ GLR }} project settings:

  1. Open {{ GL }}.
  1. Select a project.
  1. In the left-hand menu, select **Settings** → **CI/CD**.
  1. Under **Runners**, click **Expand**.
  1. Click **New project runner** and create a new {{ GLR }}.
  1. Save the value of the `Runner authentication token` parameter.

## Install {{ GLR }} on a {{ compute-full-name }} VM {#install}

1. [Create a VM](../../compute/operations/vm-create/create-linux-vm.md) from a public Ubuntu 22.04 LTS image.

1. [Connect](../../compute/operations/vm-connect/ssh.md#vm-connect) to the VM over SSH:

   ```bash
   ssh <login>@<VM_public_IP_address>
   ```

1. Install the git and jq utilities:

   ```bash
   sudo apt-get --yes install git jq
   ```

1. Add a repository with {{ GLR }} to the package manager:

   ```bash
   curl --location https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
   ```

1. Install {{ GLR }}:

   ```bash
   sudo apt-get -y install gitlab-runner
   ```

1. Register {{ GLR }}:

   ```bash
   sudo gitlab-runner register
   ```

   The command will prompt you for additional data:

   * URL of the {{ GL }} instance in `https://<domain>/` format.
   * [Previously obtained](#gitlab-token) {{ GLR }} token.
   * {{ GLR }} description.
   * Do not specify {{ GLR }} tags and the update settings (`maintenance note`).
   * `executor`: `shell`.

   Result:

   ```text
   Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!

   Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml"
   ```

## Create a runner using the management console {#create-runner}

1. Select the {{ mgl-name }} instance [created earlier](#infra).

1. Select the **Runners** tab.

1. Click **Create runner**.

1. Enter a name for the runner:
    
    * The name must be 2 to 63 characters long.
    * It may contain lowercase Latin letters, numbers, and hyphens.
    * It must start with a letter and cannot end with a hyphen.

1. Enter the [previously obtained](#gitlab-token) {{ GLR }} token.

1. Select or create a [service account](../../iam/concepts/users/service-accounts.md). The service account must have the following roles: `compute.admin`, `vpc.admin`, and `iam.serviceAccounts.user`.

1. Optionally, add labels for the runner.

1. Under **Scaling settings**, specify:

    * Maximum number of workers
    * Minimum number of workers
    * Worker downtime limit in minutes
    * Maximum number of jobs per worker
    * Maximum number of parallel jobs per worker

1. Optionally, add labels for the worker.

1. Under **{{ ui-key.yacloud.compute.instances.create.section_platform }}**, select one of the preset configurations.

1. Under **{{ ui-key.yacloud.compute.instances.create.section_storages }}**, configure the boot [disk](../../compute/concepts/disk.md):

    * Select the [disk type](../../compute/concepts/disk.md#disks_types).
    * Specify the required disk size. 

1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

1. Make sure the runner works:

    * In {{ GL }}:
      * If {{ GLR }} was created for the whole {{ GL }} instance:
          1. In the bottom-left corner, click **Admin**. 
          1. In the left-hand menu, select **CI/CD** → **Runners**.
          1. Make sure the new runner is now in the list.

      *  If {{ GLR }} was created for a project:
          1. Open the project.
          1. In the left-hand menu, select **Settings** → **CI/CD**.
          1. Under **Runners**, click **Expand**.
          1. Make sure the new runner has appeared in the **Assigned project runners** section.

    * In {{ compute-name }}, make sure that new VMs with the `runner-` prefix have appeared.

## Create a test scenario {#example} 

1. Open the {{ GL }} project.

1. Select **Build** → **Pipeline editor** in the left-hand menu. A page will open asking you to add a new file named `.gitlab-ci.yml`, in which you need to describe the scenario in [YAML](https://yaml.org/) format.

1. Add the scenario text:

    ```yaml
    build:
    stage: build
    script:
      - echo "Hello, $GITLAB_USER_LOGIN!"

    test:
    stage: test
    script:
      - echo "This job tests something"

    deploy:
    stage: deploy
    script:
      - echo "This job deploys something from the $CI_COMMIT_BRANCH branch."
    environment: production
    ```

1. Click **Commit changes**.

1. Select **Build** → **Jobs** in the left-hand menu.

1. Make sure that three jobs have the `Passed` status.

## Delete the resources you created {#clear-out}

Some resources are not free of charge. Delete the resources you no longer need to avoid paying for them:

* [{{ GL }} instance](../../managed-gitlab/operations/instance/instance-delete.md)
* [VM with {{ GLR }}](../../compute/operations/vm-control/vm-delete.md)
* [Service account](../../iam/operations/sa/delete.md)
