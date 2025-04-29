# Network security requirements

## 2. Network security {#network-security}


This section provides users with recommendations on security settings in [{{ vpc-full-name }}](../../../vpc/).


To isolate applications from each other, put resources in different [security groups](../../../vpc/concepts/security-groups.md), and, if strict isolation is required, in different [networks](../../../vpc/concepts/network.md#network). By default, internal network traffic is allowed, while traffic between networks is not. Traffic between networks is only allowed via [VMs](../../../compute/concepts/vm.md) with two network interfaces in different networks, VPN, or [{{ interconnect-full-name }}](../../../interconnect/index.yaml).

### Overview {#general}

#### 2.1 Cloud objects use a firewall or security groups {#firewall}

With built-in security groups, you can manage VM access to resources and security groups in {{ yandex-cloud }} or resources on the internet. A security group is a set of rules for incoming and outgoing traffic that can be assigned to a VM's network interface. Security groups work like a stateful firewall: they monitor the status of sessions and, if a rule allows a session to be created, they automatically allow response traffic. For a guide on how to set up security groups, see [{#T}](../../../vpc/operations/security-group-create.md). You can specify a security group in the VM settings.

You can use security groups to protect:
* VM
* [Managed databases](/services#data-platform)
* [{{ alb-full-name }}](../../../application-load-balancer/)](../../../application-load-balancer/concepts/application-load-balancer.md) [load balancers
* [{{ managed-k8s-full-name }}](../../../managed-kubernetes/)](../../../managed-kubernetes/concepts/index.md#kubernetes-cluster) [clusters

The list of available services is being extended.

You can manage network access without security groups, e.g., by using a separate VM as a firewall based on an [NGFW]({{ link-cloud-marketplace }}/products/usergate/ngfw) image from {{ marketplace-full-name }} or a custom image. Using the NGFW can be critical to customers if they need the following features:
* Logging network connections.
* Streaming traffic analysis for malicious content.
* Detecting network attacks by signature.
* Other features of conventional NGFW solutions.

Make sure that your [clouds](../../../resource-manager/concepts/resources-hierarchy.md#cloud) use any of the following:

* Security groups in each cloud object.
* A separate NGFW VM from {{ marketplace-name }}.
* [BYOI](https://en.wikipedia.org/wiki/Bring_your_own_operating_system) principle, e.g., [your own disk image](../../../compute/operations/image-create/upload.md).

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  Check if there are security groups in different objects:
  1. Open the [{{ yandex-cloud }} management console]({{ link-console-main }}) in your browser.
  1. Go to each cloud and [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) and open all resources listed in "Objects that security groups can be applied to", one by one.
  1. In the object settings, find the **Security group** parameter and make sure that at least one security group is assigned.
  1. If the parameters of each object with security group support have at least one group set, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

  Check whether the NGFW is used instead of security groups:
  1. Open the {{ yandex-cloud }} management console in your browser.
  1. Go to each cloud and folder and open all VM [disks](../../../compute/concepts/vm.md) one by one.
  1. In the disk settings, find the **{{ marketplace-short-name }}** product parameter.
  1. If the disk's **{{ marketplace-short-name }} product** parameters have one of the NGFW product names specified: Check Point CloudGuard IaaS — Firewall & Threat Prevention PAYG or UserGate NGFW, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the `ID` you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for cloud objects with no security group:

     ```bash
     export ORG_ID=<Organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for VM_ID in $(yc compute instance list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc compute instance get --id=$VM_ID --format=json | jq -r '. | select(.network_interfaces[].security_group_ids | not)' | jq -r '.id'
     done;
     done;
     done
     ```

  1. If an empty string is output, the recommendation is fulfilled. If you get the cloud resource `ID` in the output, proceed to "Guides and solutions to use".

  Check whether the NGFW is used instead of a security group:
  1. Run the command to search for the NGFW in the cloud. By default, the command searches for Checkpoint or Usergate. If you use a custom image, specify it.

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id');
     do for DISK_ID in $(yc compute disk list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc compute disk get --id=$DISK_ID --format=json | jq -r '. | select(.product_ids[0]=="f2ecl4ak62mjbl13qj5f" or .product_ids[0]=="f2eqc5sac8o5oic7m99k")' | jq -r '.id'
     done;
     done;
     done
     ```

  1. If you get the `ID` of a VM with the NGFW in the output, the recommendation is fulfilled. If you get an empty string, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**
* Apply security groups to any objects that have no group.
* To apply security groups through {{ TF }}, [set up security groups (dev/stage/prod) using {{ TF }}](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/network-sec/segmentation).
* To use the NGFW, [install](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/network-sec/checkpoint-1VM) the NGFW on your VM: Check Point.
* Refer to [this guide](https://docs.google.com/document/d/1yYwHorzkwXwIUGeG3n_K6Zo-07BVYowZJL7q2bAgVR8/edit?usp=sharing) on using the UserGate NGFW in the cloud.
* Use NGFW in [active-passive](https://github.com/yandex-cloud/yc-solution-library-for-security/blob/master/network-sec/checkpoint-2VM_active-active/README.md) mode.

#### 2.2 {{ vpc-name }} has at least one security group {#vpc-sg}

To apply security groups to your cloud objects in {{ vpc-name }}, make sure there is at least one security group. You can also create a [default security group](../../../vpc/concepts/security-groups.md#default-security-group) that will be assigned to cloud objects when connecting to [subnets](../../../vpc/concepts/network.md#subnet) if they have no security group. Make sure that each network has at least one security group.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. Open the {{ yandex-cloud }} console in your browser.
  1. Go to each cloud and then to each folder and each {{ vpc-name }}.
  1. Go to **Security groups**.
  1. If at least one security group for each {{ vpc-name }} or the default security group is found, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the `ID` you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for folders with no security group:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); \
     do echo "SG_ID: " && yc vpc security-group list --folder-id=$FOLDER_ID --format=json | jq -r '.[] | select(.id)' | jq -r '.id' && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done
     ```

  1. If each `SG_ID` combination has the `ID` specified in front of the `FOLDER_ID` of the folder it resides in, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

Create a security group in each {{ vpc-name }} with restricted access rules, so that it can be assigned to cloud objects.

#### 2.3 Security groups have no access rule that is too broad {#access-rule}

A security group lets you grant network access to absolutely any IP address on the internet as well as across all port ranges. A dangerous rule looks as follows:
* Port range: 0 to 65535 or empty.
* Protocol: Any or TCP/UDP.
* Source: CIDR.
* CIDR blocks: 0.0.0.0/0 (access from any IP address) or ::/0 (ipv6).

{% note warning %}

If no port range is set, it is considered that access is granted across all ports (0-65535).

{% endnote %}

Make sure to only allow access through the ports that your application requires to run and from the IPs to connect to your objects from.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. Open the {{ yandex-cloud }} console in your browser.
  1. Go to each cloud and then to each folder and each {{ vpc-name }}.
  1. Go to **Security groups**.
  1. If there is no security group containing network access rules that allow access through any port and from any IP address (for explanation, see above), the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the `ID` you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Find security groups with a dangerous access rule:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); \
     do echo "SG_ID: " && yc vpc security-group list --folder-id=$FOLDER_ID \
     --format=json | jq -r '.[] | select(.rules[].direction=="INGRESS" and .rules[].ports.to_port=="65535" and .rules[].cidr_blocks.v4_cidr_blocks[]=="0.0.0.0/0")' | jq -r '.id' \
     && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done
     ```

  1. If an empty value is set in `SG_ID` next to `FOLDER_ID`, the recommendation is fulfilled. If you see a non-empty `SG_ID`, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

Delete the dangerous rule in each security group or edit it by specifying trusted IPs.

#### 2.4 Access through control ports is only allowed for trusted IPs {#trusted-ip}

We recommend that you only allow access to your cloud infrastructure through control ports from trusted IP addresses. Make sure your access rules specified in the security group contain no broad rules that allow access through control ports:
* Port range: 22, 3389, or 21.
* Protocol: TCP.
* Source: CIDR.
* CIDR blocks: 0.0.0.0/0 (access from any IP address) or ::/0 (ipv6).

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. Open the {{ yandex-cloud }} console in your browser.
  1. Go to each cloud and then to each folder and each {{ vpc-name }}.
  1. Go to **Security groups**.
  1. If there is no security group containing network access rules that allow access through control ports from any IP address (for explanation, see above), the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the `ID` you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for security groups with dangerous access rules:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); \
     do echo "SG_ID: " && yc vpc security-group list --folder-id=$FOLDER_ID \
     --format=json | jq -r '.[] | select(.rules[].direction=="INGRESS" and (.rules[].ports.to_port=="22" or .rules[].ports.to_port=="3389" or .rules[].ports.to_port=="21") and .rules[].cidr_blocks.v4_cidr_blocks[]=="0.0.0.0/0")' | jq -r '.id' \
     && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done
     ```

  1. If an empty value is set in `SG_ID` next to `FOLDER_ID`, the recommendation is fulfilled. If the `SG_ID` is not empty, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

[Delete](../../../cli/cli-ref/vpc/cli-ref/security-group/index.md) the dangerous rule in each security group or specify trusted IPs.

#### 2.5 DDoS protection is enabled {#ddos-protection}

{{ yandex-cloud }} provides basic and advanced DDoS protection as well as protection at the application level with {{ sws-full-name }}. Make sure to use at least basic protection.

* [{{ sws-full-name }}](../../../smartwebsecurity/quickstart.md) is a service for protection against DDoS attacks and bots at application level L7 of the [OSI model](https://en.wikipedia.org/wiki/OSI_model). {{ sws-name }} [connects](../../../smartwebsecurity/quickstart.md) to {{ alb-full-name }}. In a nutshell, the service checks the HTTP requests sent to the protected resource against the [rules](../../../smartwebsecurity/concepts/rules.md) configured in the [security profile](../../../smartwebsecurity/concepts/profiles.md). Depending on the results of the check, the requests are forwarded to the protected resource, blocked, or sent to [{{ captcha-full-name }}](../../../smartcaptcha/index.yaml) for additional verification.
* [{{ ddos-protection-full-name }}](../../../vpc/ddos-protection/index.md) is a {{ vpc-name }} component that safeguards cloud resources from DDoS attacks. {{ ddos-protection-name }} is provided in partnership with Curator. You can enable it yourself for an external [IP address](../../../vpc/concepts/address.md) through cloud administration tools. Supported up to OSI L4.
* [Advanced](/services/ddos-protection) DDoS protection is available at OSI layers 3, 4, and 7. You can also track load and attack metrics and enable Solidwall WAF in your Curator account. To enable advanced protection, contact your manager or technical support.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  * To make sure you are using DDoS protection at the application level:

      1. In the [management console]({{ link-console-main }}), select the [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) where you want to check the {{ sws-name }} status.
      1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_smartwebsecurity }}**.
      1. Make sure you have security profiles created.
      1. If you have security profiles, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

  * To make sure you are using basic DDoS protection:

      1. In the [management console]({{ link-console-main }}), open all the created networks.
      1. Go to **IP addresses**.
      1. If all the public IP addresses have the **DDoS protection** column set to **Enabled**, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Manual check {#manual}

  Contact your account manager to make sure you have advanced DDoS protection activated. 

- Performing a check via the CLI {#cli}

  * To make sure you are using DDoS protection at the application level, run this command:

      ```bash
      yc smartwebsecurity security-profile list
      ```

      If the command returns information about the existing security profiles, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

  * To make sure you are using basic DDoS protection:

      1. See what organizations are available to you and write down the `ID` you need:

           ```bash
           yc organization-manager organization list
           ```

      1. Run the command below to search for IP addresses with no DDOS protection:

           ```bash
           export ORG_ID=<organization ID>
           for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
           do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); \
           do echo "Address_ID: " && yc vpc address list --folder-id=$FOLDER_ID \
           --format=json | jq -r '.[] | select(.external_ipv4_address.requirements.ddos_protection_provider=="qrator" | not)' | jq -r '.id' \
           && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
           done;
           done
           ```

      1. If an empty value is set in `Address_ID` next to `FOLDER_ID`, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

* [How to create a security profile in {{ sws-name }}](../../../smartwebsecurity/operations/profile-create.md)
* All [materials](../../../vpc/ddos-protection/index.md) about DDoS protection in {{ yandex-cloud }}.

#### 2.6 Protected remote access is used {#secure-access}

To enable administrators to establish remote connections to your cloud resources, use one of the following:
* Site-to-site VPN between a remote site, e.g., your office, and a cloud. As a remote access gateway, use a VM featuring a site-to-site VPN based on an [image]({{ link-cloud-marketplace }}?categories=network) from {{ marketplace-name }}.

  **Setup options**:
  * [Creating an IPsec VPN tunnel using the strongSwan](../../../tutorials/routing/ipsec/index.md).
  * [Creating a site-to-site VPN connection to {{ yandex-cloud }} using {{ TF }}](https://github.com/yandex-cloud-examples/yc-site-to-site-vpn-with-ipsec-strongswan).
  * Client VPN between remote devices and {{ yandex-cloud }}. As a remote access gateway, use a VM featuring a client VPN based on an [image]({{ link-cloud-marketplace }}?categories=network) from {{ marketplace-name }}.

  See the guide in [Creating a VPN connection using OpenVPN](../../../tutorials/routing/openvpn.md). You can also use certified data cryptographic security tools.
* Dedicated private connection between a remote site and {{ yandex-cloud }} using {{ interconnect-name }}.

To access the infrastructure using control protocols (such as SSH or RDP), create a bastion VM. You can do this using a free [Teleport](https://goteleport.com/) solution. Access to the bastion VM or VPN gateway from the internet must be restricted.

For better control of administrative actions, we recommend that you use PAM (Privileged Access Management) solutions that support administrator session logging (for example, Teleport). For SSH and VPN access, we recommend that you avoid using passwords and use public keys, X.509 certificates, and SSH certificates instead. When setting up SSH for your VMs, we recommend that you use the SSH certificates (including for the SSH host).

To access web services deployed in the cloud, use TLS version 1.2 or higher.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. Open the {{ yandex-cloud }} console in your browser.
  1. Open all created networks.
  1. Go to the **Route tables** section.
  1. If routes to remote sites' private networks through VMs with a VPN gateway are found, the recommendation is fulfilled.
  1. Check the VMs in each cloud for VPN gateways. In addition, check if their security groups have open ports for the VPN.

- Manual check {#manual}

  Contact your account manager to find out if you have {{ interconnect-name }} activated. If yes, check if remote access is used.

{% endlist %}

#### 2.7 Employees use {{ cloud-desktop-full-name }} for remote access {#use-cloud-desktop}

{{ cloud-desktop-full-name }} is a virtual [desktop](../../../cloud-desktop/concepts/desktops-and-groups.md) infrastructure management service.

Use this service to:

* Quickly create virtual workspaces for new employees.
* Securely connect remote employees to the corporate network.
* Allow your employees to work from any modern internet-enabled device, including a privately owned one ([BYOD](https://en.wikipedia.org/wiki/Bring_your_own_device)).
* Manage desktop computing resources.
* Administer desktops remotely.
* Create desktop groups with the same computing resources and cloud [network](../../../vpc/concepts/network.md).

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder you want to check for the presence of [desktops](../../../cloud-desktop/concepts/desktops-and-groups.md).
  1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_cloud-desktop }}**.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/display.svg) **{{ ui-key.yacloud.vdi.label_desktops }}**.
  1. If the list contains at least one created desktop, the recommendation is fulfilled; otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

1. [Create a desktop group](../../../cloud-desktop/operations/desktop-groups/create.md).
1. If you have any specific OS configuration requirements, you can use your own OS image by following the [{#T}](../../../cloud-desktop/operations/images/create-from-compute-linux.md) guide or [create](../../../cloud-desktop/operations/images/create-from-desktop.md) an image based on the existing desktop and reuse it for the group.
1. After you create a desktop group, the administrator can [create](../../../cloud-desktop/operations/desktops/create.md) the required number of desktops and assign users for them. Alternatively, the desktop group users can use the [user desktop showcase](../../../cloud-desktop/concepts/showcase.md) to get a desktop by themselves.

#### 2.8 Secure Yandex Browser is used for remote access to {{ cloud-desktop-name }} {#use-yandex-browser}

Employees working remotely via {{ cloud-desktop-name }} should use the [Secure Yandex Browser](https://browser.yandex.ru/corp) to access the corporate resources. This requirement enforces data security for protection against [phishing](https://en.wikipedia.org/wiki/Phishing), malicious websites, and data leaks. The browser has built-in tools for traffic encryption, blocking of dangerous resources, and integration with corporate authentication systems.

#### 2.9 Outbound internet access control is performed {#outgoing-access}

Possible options for setting up outbound internet access:
* [Public IP address](../../../vpc/concepts/address.md#public-addresses). Assigned to a VM according to the one-to-one NAT rule.
* [Egress NAT (NAT gateway)](../../../vpc/operations/create-nat-gateway.md). Enables internet access for a subnet through a shared pool of {{ yandex-cloud }} public IP addresses. We do not recommend using an Egress NAT for critical interactions because the NAT gateway's IP address can be used by several clients at the same time. This feature must be taken into account when modeling threats for your infrastructure.
* [NAT instance](../../../tutorials/routing/nat-instance/index.md). The NAT function is performed by a separate VM. You can create this VM using a [NAT instance]({{ link-cloud-marketplace }}/products/yc/nat-instance-ubuntu-18-04-lts) image from {{ marketplace-name }}.

**Comparison of internet access methods**:

#|
|| Public IP address | Egress NAT | NAT instance ||
|| **Advantages:** | **Advantages:** | **Advantages:** ||
||
* No setup required
* Dedicated IP address for each VM
|
* No setup required
* Only works for outgoing connections
|
* Traffic filtering on a NAT instance
* Using your own firewall
* Effective use of IP addresses ||
|| **Disadvantages:** | **Disadvantages:** | **Disadvantages:** ||
||
* It might be unsafe to expose a VM directly to the internet
* Cost of reserving each IP address |
* Shared pool of IP addresses
* The feature is at the [Preview](../../../overview/concepts/launch-stages.md) stage; therefore, it is not recommended for production environments |
* Setup required
* VM cost (vCPU, RAM, disk space) ||
|#

Regardless of which option you select for setting up outbound internet access, be sure to limit traffic using one of the mechanisms described above. To build a secure system, use static IP addresses because they can be added to the list of exceptions of the receiving party's firewall.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. Open the {{ yandex-cloud }} console in your browser.
  1. Go to the appropriate folder.
  1. Go to **IP addresses**.
  1. If all the public IP addresses have the **DDoS protection** column set to **Enabled**, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the `ID` you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for all VMs with public IPs:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id');
     do echo "VM_ID: " && yc compute instance list --folder-id=$FOLDER_ID --format=json | jq -r '.[] | select(.network_interfaces[].primary_v4_address.one_to_one_nat.address)' | jq -r '.id' \
     && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done
     ```

  1. If an empty value is set in `VM_ID` next to `FOLDER_ID`, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".
  1. Run the command below to see if there is Egress NAT (NAT gateway):

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); \
     do echo "NAT_GW: " && yc vpc gateway list --folder-id=$FOLDER_ID --format=json | jq -r '.[] | select(.id)' | jq -r '.id' && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done
     ```

  1. If an empty value is set in `NAT_GW` next to `FOLDER_ID`, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".
  1. Run the command below to see if there is a NAT instance:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id');
     do for DISK_ID in $(yc compute disk list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc compute disk get --id=$DISK_ID --format=json | jq -r '. | select(.product_ids[0]=="fd8v7ru46kt3s4o5f0uo")' | jq -r '.id'
     done;
     done;
     done
     ```

  1. If an empty string is output, the recommendation is fulfilled. If you see the NAT instance `ID`, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**
* If any VM has public IPs, make sure they are required. Otherwise, delete an external IP address in the VM settings.
* If any NAT-Gateway is found, make sure it is required. Otherwise, delete it.
* If any NAT instance is found, make sure it is required. Otherwise, delete it.

#### 2.10 DNS queries are not provided to third-party recursive resolvers {#recursive-resolvers}

To increase fault tolerance, some traffic may be routed to third-party recursive resolvers. To avoid this, contact [support](../../../support/overview.md).
