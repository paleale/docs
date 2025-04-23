# Getting started with {{ vpc-short-name }}

Create a [cloud network and subnet](concepts/network.md) in an empty cloud and reserve a [static public IP address](concepts/address.md#public-addresses) for your VM using {{ vpc-name }}. Your cloud resources will be connected to the subnet and the static public IP address will remain reserved even if you stop or delete the VM it was associated with. You will be able to associate this address with another VM later.

{% note info %}

You can automatically create a cloud network and subnets in all [availability zones](../overview/concepts/geo-scope.md) when [creating a folder](../resource-manager/operations/folder/create.md) or [creating a VM](../compute/quickstart/quick-create-linux.md).

{% endnote %}

## Getting started {#before-begin}

1. Log in to the [management console]({{ link-console-main }}) or sign up. If not signed up yet, navigate to the management console and follow the on-screen instructions.
1. Go to [{{ billing-name }}]({{ link-console-billing }}) and make sure you have a [billing account](../billing/concepts/billing-account.md) linked and its status is `ACTIVE` or `TRIAL_ACTIVE`. If you do not have a billing account yet, [create one](../billing/quickstart/index.md#create_billing_account).
1. If you do not have a folder yet, [create one](../resource-manager/operations/folder/create.md). While creating a folder, you can also create a default virtual network with subnets in all availability zones.

## Create a cloud network {#create-network}

To create a cloud network:

1. In the [management console]({{ link-console-main }}), select a folder where you want to create your cloud network.
1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
1. Click **{{ ui-key.yacloud.vpc.networks.button_create }}**.
1. Enter a name for the network, e.g., `test-network`.
1. Click **{{ ui-key.yacloud.vpc.networks.create.button_create }}**.

## Create a subnet {#create-subnet}

Create a subnet where cloud resources will get [internal IP addresses](concepts/address.md#internal-addresses):

1. Click the name of the cloud network you created.
1. Click ![image](../_assets/console-icons/plus.svg) **{{ ui-key.yacloud.vpc.network.overview.button_create_subnetwork }}**.
1. Specify the subnet name, e.g., `test-subnet-1`.
1. Select an availability zone from the drop-down list. Any zone from the list will be fine for the first subnet.
1. Enter the subnet CIDR: its IP address and mask, e.g., `10.10.0.0/24`. For more information about subnet IP address ranges, see [Cloud networks and subnets](concepts/network.md).
1. Click **{{ ui-key.yacloud.vpc.subnetworks.create.button_create }}**.

## Reserve a static public IP address {#reserve-address}

Reserve a static public IP address for your VM. You can assign this address to any VM and it will not change when the VM is stopped.

{% note warning %}

You are charged for the reserved static public IP address even if it is not associated with any VM. For more information, see the [{{ vpc-name }} pricing policy](pricing.md).

{% endnote %}

To reserve an IP address:

1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
1. Go to **{{ ui-key.yacloud.vpc.switch_addresses }}**.
1. Click **{{ ui-key.yacloud.vpc.addresses.button_create }}**.
1. In the window that opens, select the availability zone where you created the subnet in the previous step.
1. Select the **{{ ui-key.yacloud.common.field_ddos-protection-provider }}** option if you want to [protect your cloud resources against DDoS attacks](ddos-protection/index.md).
1. Click **{{ ui-key.yacloud.vpc.addresses.popup-create_button_create }}**.

## Delete a subnet and static public IP address {#delete-resources}

If you no longer need the subnet, [delete it](operations/subnet-delete.md).

If the reserved address is not associated with any resource, you can [delete](operations/address-delete.md) it.

## What's next {#what-is-next}

[Create a new VM](../compute/operations/vm-create/create-linux-vm.md), connect it to a subnet, and assign it a reserved public IP address.