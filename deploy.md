---

copyright:
  years: 2026
lastupdated: "2026-01-02"

keywords:

subcollection: pattern-network-vpc-sdwan

---

{site.data.keyword.attribute-definition-list}}


# Deploying VeloCloud SD-WAN Virtual Edge
{: #deploying}

This section details the manual deployment process using the IBM Cloud Console, as provided in the specialized guide for Arista VeloCloud on IBM Cloud VPC.
{: shortdesc}


## Prerequisites and Resource Preparation
{: #prereqs}

Before starting, gather these essentials:
- Obtain VeloCloud SDWAN Appliance Image like **edge-VC_KVM_GUEST-x86_64-5.2.4.3-9710571-R5243-20250404-GA-ed1b3e9fc3-updatable-ext4.qcow2.gz** (Normally ~370 MB in .gz format.)
- **Obtain VeloCloud Registration Key and URL**: Obtain a valid VeloCloud registration key and URL to activate from Orchestrator.
    - Log into Orchestrator and then create new VCE instance. After that you can get Activation Key.
- **Configuration Files**: Prepare a basic USERDATA (cloud-init) information for initial setup including hostname, URL and registration key.

```
#cloud-config

hostname: vce01

password: Pa$$w0rd!

chpasswd: { expire: False }

ssh_pwauth: True

ssh_authorized_keys:

    - ssh-rsa ……………..1s92C94mHTcEtveX5FnUCSMw==

velocloud:

  vce:

    vco: <orchestrater URL>

    activation_code: BPSX-GDHL-5JJW-KA74

    vco_ignore_cert_errors: true

runcmd:

  - 'ip route add default via <eth2 (WAN Link) gateway IP> dev eth2'
```
{: codeblock}

- **IBM Cloud Resources**: Ensure you have a VPC, 3 subnets in same zone with /27 CIDR, a floating IP, and an object storage bucket to create a custom image in the desired region.
- **SSH Key**: Generate or use an existing SSH key pair for secure access.
- **Tools**: Access to the IBM Cloud console and a way to upload files (e.g., via CLI or web interface).


## Obtaining the VeloCloud Image
{: #vce-image}

To get the VeloCloud image, connect with VeloCloud Orchestrator (VCO) administrator and get the boot image for IBM Cloud.

For Virtual Edge Images (like in a Lab):

- **Contact Your Provider**:  For virtualized VeloCloud SD-WAN Edge deployments (like those for testing or Labs), consult your provider or documentation for access to virtual machine images (like KVM or OVA files).
- This image is optimized for KVM-based environments like IBM Cloud VPC.

## Uploading and Importing Custom Image
{: #custom-image}

IBM Cloud requires custom images to be imported via object storage:

1. Create or select an object storage bucket in your target region (e.g., **us-south**).
2. Extract the .tar.gz file and upload the **.qcow2** file to the bucket using the IBM Cloud console or CLI:
3. In the IBM Cloud console, go to VPC Infrastructure > Compute > Custom images.
4. Click Import custom image.
5. Provide:
   - **Name**: e.g., velocloud-edge.
   - **Region**: Match your VPC region.
   - **Image Source**: Select the uploaded **.qcow2** from your bucket.
   - **Operating System**: Choose **Ubuntu 24.04** (as recommended for compatibility).
6. Initiate the import; it may take 5-10 minutes to complete.

Once imported, the image appears in your custom images list, ready for instance creation.

## Creating the VeloCloud Edge Instance
{: #create-vce-instance}



With the image ready, deploy the instance:

1. Navigate to **VPC Infrastructure > Compute > Virtual server instances**.
2. Click **Create +** to start a new instance.
3. Configure the basics:
   - **Name**: e.g., **vce01**
   - **VPC**: Select your existing VPC.
   - **Location/Zone**: Choose the zone matching your subnets.
   - **Image**: Select the imported **velocloud-edge** custom image.
   - **Profile**: Start with **bx2-4x16** (4 vCPUs, 16 GB RAM) for testing; scale up for production (e.g., **bx2-8x32** or higher).
   - **SSH Keys**: Add your public SSH key for console access.
4. Add network interfaces:
   - **Primary Interface**: Attach to the public subnet (for management and external access).
   - **Create total 3 Network interfaces coming from 3 subnets in same zone**.
5. Under **Advanced > Boot Configuration**, provide user data for automated setup. This automates registration application and initial config during boot.
6. Review and create the instance. Monitor the provisioning status in the console.
   1. Eth0 – GE1 – GRE Redundancy
   2. Eth1 – GE2 – GRE Redundancy
   3. Eth2 – GE3 – WAN Interface
   - Once the machine is build, login to serial console with root and the password provided in USERDATA config.
   - Run ifconfig and note the interface names
   - Open /etc/config/network and check the interfaces with corresponding GE interface

### Attaching a Floating IP and Initial Access
{: #floating-ip}

To enable external management:

1. Once the instance is running, go to **Network > IP addresses > Floating IPs**.
2. Reserve a new floating IP in the same region
3. Attach it to the instance's 3rd network interface (eth2 – GE3).

   **Note**: IBM Cloud supports one floating IP per instance

Access the VeloCloud Edge through IBM Cloud Serial Console:

- **Serial Console**: Open serial console with credentials: root and password as described in cloud-init config file; change immediately
- Verify setup with CLI commands

If issues arise, check cloudinit logs for metadata, license, and script errors.

- **Scaling**: Monitor VM resources (e.g., 2 vCPUs/8 GB min); upgrade profiles as traffic grows.
- **Backup**: Regularly export configs and use IBM snapshots for images.


## Day 2 Operations and Security
{: #day2}

### Performance Optimiztion
{: #perf-optimization}

Arista VeloCloud is a latency-sensitive application. To achieve the highest performance, the underlying IBM VPC host settings should ideally have Intel Virtualization Technology (Intel VT) enabled and Hyper-threading deactivated. VSI profiles must maintain 100% reserved CPU and memory resources to ensure DMPO stability.

### Security Posture
{: #security-posture}

Security is enforced at the VCE level using Arista's integrated firewall services.
   1.	Network Isolation: Use IBM Security Groups to permit Arista VeloCloud DMPO traffic (UDP 2426, 2427) and management traffic (TCP 443) on the WAN interface.
   2.	IDS/IPS: Enable the integrated Suricata-based engine via the VCO to inspect all traffic traversing the VPC, ensuring internal threats are identified and neutralized.

## Conclusions and Recommendations
{: #conclusions}

The integration of Arista VeloCloud SD-WAN into IBM Cloud VPC provides a highly resilient, enterprise-grade networking solution. By following this technical pattern—leveraging custom image imports, cloud-native L3 failover via NLB Route Mode, and explicit traffic insertion via Custom Routing Tables—organizations can achieve seamless client-to-cloud connectivity that is both automated and secure.
