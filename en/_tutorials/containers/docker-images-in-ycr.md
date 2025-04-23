# Storing {{ mgl-full-name }} Docker images in {{ container-registry-full-name }}


[{{ container-registry-name }}](https://docs.gitlab.com/ee/user/packages/container_registry/) is integrated in {{ GL }}. It enables you to store Docker images for each of your projects in {{ GL }}.

You can use [{{ container-registry-full-name }}](../../container-registry/index.yaml) instead of {{ GL }} {{ container-registry-name }}. This service enables you to store Docker images in the cloud or distribute them across {{ yandex-cloud }} managed services, for example, [{{ managed-k8s-full-name }}](../../managed-kubernetes/index.yaml) or [{{ mgl-full-name }}](../../managed-gitlab/index.yaml).

Storing images from {{ GL }} projects in {{ container-registry-full-name }} has several benefits:

* {{ GL }} {{ container-registry-name }} stores images and tags on the [{{ GL }} instance](../../managed-gitlab/concepts/index.md#instance) disk. When you run out of disk space, you get the HTTP 500 error, and the instance becomes unavailable. You can recover the instance only by contacting tech support.

   {{ container-registry-full-name }} stores images and tags in [registries](../../container-registry/concepts/registry.md) for which [individual quotas](../../container-registry/concepts/limits.md) are allocated. Because of this, accumulating Docker images and tags does not affect the space available on the instance disk.

* The images are still available in {{ container-registry-full-name }}, even if {{ mgl-name }} is not.

* {{ container-registry-full-name }} supports the [Docker image vulnerability scanner](../../container-registry/concepts/vulnerability-scanner.md). Use the scanner to detect vulnerabilities and fix them before deploying your application.

To set up storage of {{ mgl-name }} Docker images in {{ container-registry-full-name }}:

1. [Create a {{ GL }} instance](#create-gitlab).
1. [Configure {{ GL }}](#configure-gitlab).
1. [Create a test application](#app-create).
1. [Create a {{ GLR }}](#runners).
1. [Create {{ GL }} environment variables](#add-variables).
1. [Create the CI script configuration file](#add-ci).
1. [Check the result](#check-result).
1. [Enable a Docker image lifecycle policy](#lifecycle-policy).
1. (Optional) [Scan your Docker images for vulnerabilities](#vulnerability-scanner).

If you have already set up your {{ mgl-full-name }} instance for Continuous Integration (CI), check that you have prepared the [infrastructure for Docker images](#deploy-infrastructure). That done, begin the setup by [creating environment variables](#add-variables).

If you no longer need the resources you created, [delete them](#clear-out).

{% note info %}

By default, {{ GL }} {{ container-registry-name }} is disabled when creating an {{ mgl-name }} instance.

{% endnote %}

## Getting started {#before-you-begin}

{% include [before-you-begin](../_tutorials_includes/before-you-begin.md) %}

### Required paid resources {#paid-resources}

Infrastructure support costs include fees for the following resources:

* Disks and continuously running VMs (see [{{ compute-full-name }} pricing](../../compute/pricing.md)).
* Using a dynamic public IP address (see [{{ vpc-full-name }} pricing](../../vpc/pricing.md)).
* Storing the Docker images you created and a vulnerability scanner, if [activated](#vulnerability-scanner) (see [{{ container-registry-name }} pricing](../../container-registry/pricing.md)).
* Using a {{ managed-k8s-name }} master (see [{{ managed-k8s-name }} pricing](../../managed-kubernetes/pricing.md)).

### Set up your infrastructure {#deploy-infrastructure}

{% list tabs group=instructions %}

- Manually {#manual}

   1. If you do not have a [network](../../vpc/concepts/network.md#network) yet, [create one](../../vpc/operations/network-create.md).
   1. If you do not have any [subnets](../../vpc/concepts/network.md#subnet) yet, [create them](../../vpc/operations/subnet-create.md) in the [availability zones](../../overview/concepts/geo-scope.md) where your [{{ managed-k8s-full-name }} cluster](../../managed-kubernetes/concepts/index.md#kubernetes-cluster) and [node group](../../managed-kubernetes/concepts/index.md#node-group) will be created.
   1. [Create a service account](../../iam/operations/sa/create.md) named `account-for-container-registry` with the following [roles](../../iam/concepts/access-control/roles.md) for the folder:

      * `{{ roles-editor }}`
      * `{{ roles-cr-pusher }}`
      * `{{ roles-cr-puller }}`

   1. [Create a {{ managed-k8s-name }} cluster](../../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-create.md#kubernetes-cluster-create) with a basic master and create a [node group](../../managed-kubernetes/operations/node-group/node-group-create.md). When creating a cluster, specify the service account you created previously.
   1. Configure a security group for the [{{ managed-k8s-name }} cluster](../../managed-kubernetes/operations/connect/security-groups.md) and [{{ mgl-name }} instance](../../managed-gitlab/operations/configure-security-group.md).

   1. [Create a registry in {{ container-registry-full-name }}](../../container-registry/operations/registry/registry-create.md).

- {{ TF }} {#tf}

   1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
   1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
   1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
   1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

   1. Download the [container-registry-and-gitlab.tf](https://github.com/yandex-cloud-examples/yc-gitlab-cr-integration/blob/main/container-registry-and-gitlab.tf) configuration file to the same working directory.

      This file describes:

      * [Network](../../vpc/concepts/network.md#network).
      * [Subnet](../../vpc/concepts/network.md#subnet).
      * [Security group](../../vpc/concepts/security-groups.md) and rules required for the {{ mgl-name }} instance and [{{ managed-k8s-full-name }} cluster](../../managed-kubernetes/concepts/index.md#kubernetes-cluster).
      * {{ managed-k8s-name }} cluster with a basic master.
      * [Node group for the cluster](../../managed-kubernetes/concepts/index.md#node-group).
      * [Service account](../../iam/concepts/users/service-accounts.md) required for the {{ managed-k8s-name }} cluster and node group.
      * {{ container-registry-full-name }} registry.

   1. Specify the following in the `container-registry-and-gitlab.tf` file:

      * [Cloud ID](../../resource-manager/operations/cloud/get-id.md).
      * [Folder ID](../../resource-manager/operations/folder/get-id.md).
      * [{{ k8s }} version](../../managed-kubernetes/concepts/release-channels-and-updates.md) for the {{ managed-k8s-name }} cluster and node groups.

   1. Make sure the {{ TF }} configuration files are correct using this command:

      ```bash
      terraform validate
      ```

      If there are any errors in the configuration files, {{ TF }} will point them out.

   1. Create the required infrastructure:

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

{% include [create-gitlab](../../_includes/managed-gitlab/create-gitlab.md) %}

{% include [configure-gitlab](../../_includes/managed-gitlab/initialize.md) %}

## Create a test application {#app-create}

Create a test application that can be deployed in a {{ managed-k8s-name }} cluster. To do this, add the following to the `Dockerfile` project:

   1. Log in to {{ GL }}.
   1. Open the {{ GL }} project.
   1. Click ![image](../../_assets/console-icons/plus.svg) in the repository navigation bar and select **New file** from the drop-down menu.
   1. Name the file as `Dockerfile` and add the following code to it:

      ```Dockerfile
      FROM alpine:3.10
      CMD echo "Hello"
      ```

   1. Add a comment in the **Commit message** field: `Dockerfile for a test application`.
   1. Click **Commit changes**.

{% include [create glr](../../_includes/managed-gitlab/k8s-runner.md) %}

## Create {{ GL }} environment variables {#add-variables}

To allow {{ mgl-name }} to save Docker images and their tags in {{ container-registry-full-name }}, create the [{{ GL }} environment variables](https://docs.gitlab.com/ee/ci/variables/index.html):

1. {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

1. Create a [static key](../../iam/concepts/authorization/access-key.md) for the `account-for-container-registry` service account [you created earlier](#before-you-begin):

   ```bash
   yc iam key create --service-account-name account-for-container-registry -o key.json
   ```

   The key is saved in the `key.json` file in the directory you ran the command in.

1. Open your project in {{ GL }}.

1. Go to **Settings** in the left-hand panel, then select **CI/CD** from the drop-down list.

1. Click **Expand** next to **Variables**.

1. Add environment variables with the protection option disabled:

   | **Variable**        | **Its value**                    |
   | --------------------- | ---------------------------------- |
   | `CI_REGISTRY`         | `{{ registry }}/<registry_ID>`. Specify the ID of the {{ container-registry-full-name }} registry that you created previously. |
   | `CI_REGISTRY_KEY`     | `key.json` file contents.       |

   To add a variable:

   1. Click **Add variable**.
   1. In the window that opens, specify the variable name in the **Key** field and its value in the **Value** field.
   1. Disable the **Protect variable** option.
   1. Click **Add variable**.

## Create the CI script configuration file {#add-ci}

To build images from a Dockerfile without Docker, use [kaniko](https://github.com/GoogleContainerTools/kaniko).

To publish Docker images from your {{ GL }} project in {{ container-registry-full-name }}, create a CI script:

1. Open the `gitlab-test` project.
1. Click ![image](../../_assets/console-icons/plus.svg) in the repository navigation bar and select **New file** from the drop-down menu.
1. Name your file `.gitlab-ci.yml`. Add to it the steps to build a Docker image and push it to {{ container-registry-full-name }}:

   {% cut ".gitlab-ci.yml" %}

   ```yaml
   build:
      stage: build
      # Using `kaniko` to create a container inside another container for enhanced security.
      image:
         name: gcr.io/kaniko-project/executor:debug
         entrypoint: [""]
      script:
         - mkdir -p /kaniko/.docker
         # Upload the container image to the registry. The image is tagged with the commit hash.
         - echo "{\"auths\":{\"$CI_REGISTRY\":{\"auth\":\"$(echo -n "json_key:${CI_REGISTRY_KEY}" | base64 | tr -d '\n' )\"}}}" > /kaniko/.docker/config.json
         - >-
            /kaniko/executor
            --context "${CI_PROJECT_DIR}"
            --dockerfile "${CI_PROJECT_DIR}/Dockerfile"
            --destination "${CI_REGISTRY}/${CI_PROJECT_PATH}:${CI_COMMIT_SHORT_SHA}"
   ```

   {% endcut %}

   This file includes the variables:

   * `CI_REGISTRY` and `CI_REGISTRY_KEY`: Added to GitLab in the [previous step](#add-variables).
   * `CI_PROJECT_DIR`, `CI_PROJECT_PATH`, and `CI_COMMIT_SHORT_SHA`: [Preset in GitLab](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html).

1. Add a comment in the **Commit message** field: `Create a CI pipeline`.
1. Click **Commit changes**.

## Check the result {#check-result}

Each commit is followed by a build script. To check the script execution results:

1. Select **Build** on the left-hand panel in the `gitlab-test` project and select **Pipelines** from the drop-down menu.

1. Make sure the `build` step gets the `passed` status. This means that the CI script has been executed successfully.

1. Go to the [management console]({{ link-console-main }}/), then open the {{ container-registry-full-name }} registry.

   If the script completes successfully, a new repository is added to the registry. New Docker images from the {{ GL }} project are added to this repository at each commit.

## Enable a Docker image lifecycle policy {#lifecycle-policy}

To avoid storing outdated Docker images and their tags, configure a [Docker image lifecycle policy](../../container-registry/concepts/lifecycle-policy.md). The policy manages the images stored in your [{{ container-registry-name }} repository](../../container-registry/concepts/repository.md) to free up its space in a timely manner. This way, you no longer pay for [storing outdated images](../../container-registry/pricing.md#prices-storage).

To create a policy, [follow the guide](../../container-registry/operations/lifecycle-policy/lifecycle-policy-create.md).

When using the policy:

* External {{ container-registry-name }} and Docker image lifecycle policies [affect CI script performance](https://docs.gitlab.com/ee/user/packages/container_registry/reduce_container_registry_storage.html#use-with-external-container-registries).

* The policy imposes a [limit](../../container-registry/concepts/limits.md#container-registry-limits) on the maximum number of images that you can check per policy run. If the number of images in your {{ container-registry-name }} repository exceeds this limit, run the policy several times. You can check all your images in this manner.

## Scan your Docker images for vulnerabilities {#vulnerability-scanner}

To detect vulnerabilities in your Docker images, you can additionally activate a [vulnerability scanner](../../container-registry/concepts/vulnerability-scanner.md) in {{ container-registry-full-name }}. The scanner checks the versions of packages installed in your images against [CVE](https://cve.mitre.org/) vulnerability databases.

To enable scanning, expand your {{ GL }} project's CI script:

1. Open the `gitlab-test` project.
1. Open the `.gitlab-ci.yml` file.
1. Add to it the steps for vulnerability scanning of your Docker image:

   {% cut ".gitlab-ci.yml" %}

   ```yaml
   stages:
      - build
      - test

   <build_block_previously_added_to_file>

   container_scanning_free_yc:
      stage: test
      # Using the jq utility to search for ID and write logs.
      image: 
         name: pindar/jq
         entrypoint: [""]
      artifacts:
         when: always
         paths:
            - gl-container-scanning-report-yc.json
      variables:
         # Specify the ID of the registry you previously created.
         CI_REGISTRY_ID: "<registry_ID>"
      script:
         - export CI_COMMIT_SHORT_SHA=${CI_COMMIT_SHORT_SHA}
         # Installing Yandex Cloud CLI.
         - curl https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash -s -- -a && cp /root/yandex-cloud/bin/yc /usr/bin/
         # Start of scanning.
         - echo "Scanning image ${CI_REGISTRY}/${CI_PROJECT_PATH}:${CI_COMMIT_SHORT_SHA}..."
         - export IMAGE_ID=$(yc container image list --registry-id $CI_REGISTRY_ID --format=json | jq -r --arg CI_COMMIT_SHORT_SHA $CI_COMMIT_SHORT_SHA '.[] | select(.tags[0]==$CI_COMMIT_SHORT_SHA) | .id ')
         # Logging.
         - export SCAN_RESULT=$(yc container image scan $IMAGE_ID --format=json)
         - export CRIT_VULN=$(echo $SCAN_RESULT | jq -r '.vulnerabilities.critical // 0')
         - export HIGH_VULN=$(echo $SCAN_RESULT | jq -r '.vulnerabilities.high // 0')
         - export SCAN_ID=$(echo $SCAN_RESULT | jq -r '.id')
         - echo "Scan results:"
         - yc container image list-vulnerabilities --scan-result-id="${SCAN_ID}" --format json | jq -r '.[] | select(.severity=="CRITICAL", .severity=="HIGH")'
         - yc container image list-vulnerabilities --scan-result-id="${SCAN_ID}" --format json | jq -r '.[] | select(.severity=="CRITICAL", .severity=="HIGH")' > gl-container-scanning-report-yc.json
         # Checking the result.
         - (( SUM = $CRIT_VULN + $HIGH_VULN )) && (( RES = (SUM >= 1) )) && echo $RES && echo "image has $CRIT_VULN critical vulnerabilities and $HIGH_VULN high vulnerabilities" && exit 1 || echo "image has no high or critical vulnerabilities" exit 0
   ```

   {% endcut %}

1. Add a comment in the **Commit message** field: `Turn on a vulnerability scanner`.
1. Click **Commit changes**. After that, the updated script will run.

To make sure that the image scan was successful:

1. Select **Build** on the left-hand panel in the `gitlab-test` project, and then select **Pipelines** from the drop-down menu.
1. Make sure that the `build` and `test` steps got the `passed` status. This means that the CI script has been executed successfully.
1. Go to the [management console]({{ link-console-main }}/), then open the {{ container-registry-full-name }} registry.
1. Open your repository with Docker images from the {{ GL }} project.
1. Go to the directory with the {{ GL }} project name.
1. Make sure that the **{{ ui-key.yacloud.cr.image.label_scan-status }}** column includes **{{ ui-key.yacloud.cr.registry.label_scan-status-READY }}**.
1. In the **{{ ui-key.yacloud.cr.image.label_last-scan-time }}** column, click the link with the scan time.

   You will see the scan results. If vulnerabilities were found in the images, you will see them in the results.

## Delete the resources you created {#clear-out}

If you no longer need the resources you created, delete them:

1. [Delete the {{ mgl-name }} instance](../../managed-gitlab/operations/instance/instance-delete.md) or the [created VM with the {{ GL }} image](../../compute/operations/vm-control/vm-delete.md).
1. [Delete all Docker images](../../container-registry/operations/docker-image/docker-image-delete.md) from the {{ container-registry-name }} registry.

Delete the other resources depending on how they were created:

{% list tabs group=instructions %}

- Manually {#manual}

   1. [Delete the {{ managed-k8s-name }} cluster](../../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-delete.md).
   1. If you reserved a public IP address for the {{ managed-k8s-name }} cluster, [delete it](../../vpc/operations/address-delete.md).
   1. [Delete the service accounts](../../iam/operations/sa/delete.md).
   1. [Delete the {{ container-registry-name }} registry](../../container-registry/operations/registry/registry-delete.md).
   1. [Delete the subnets](../../vpc/operations/subnet-delete.md) and [network](../../vpc/operations/network-delete.md).

- {{ TF }} {#tf}

   {% include [terraform-clear-out](../../_includes/mdb/terraform/clear-out.md) %}

{% endlist %}
