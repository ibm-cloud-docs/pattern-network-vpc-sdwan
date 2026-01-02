---

# The YAML header is required. For more information about the YAML header, see
# https://test.cloud.ibm.com/docs-internal/writing?topic=writing-reference-architectures

copyright:
  years: 2026
lastupdated: "2026-01-02"

keywords: # Not typically populated

subcollection: pattern-network-vpc-sdwan

# The release that the reference architecture describes
version: 1.0


docs: https://cloud.ibm.com/docs/solution-guide

# use-case from 'code' column in
# https://github.ibm.com/digital/taxonomy/blob/main/topics/topics_flat_list.csv

content-type: reference-architecture

---


{{site.data.keyword.attribute-definition-list}}




# Solution Overview: Arista VeloCloud SD-WAN on IBM Cloud VPC
{: #vce-arch}
{: toc-content-type="reference-architecture"}

Arista VeloCloud SD-WAN transforms unpredictable broadband or hybrid transport networks into enterprise-class connections, assuring application performance via its distributed Cloud Gateway and Orchestrator infrastructure. In the IBM Cloud context, the VCE functions as the Cloud Gateway, extending optimized connectivity to applications hosted within the VPC.
The core function of the VCE deployment pattern is threefold:
   1.	Overlay Establishment: Creating secure, optimized VCRP-based DMPO tunnels between branch office Arista VeloCloud Edges and the VCE in the VPC, handling Branch-to-Cloud and Branch-to-Branch traffic.
   2.	Traffic Insertion: Implementing precise IBM VPC Custom Routing Tables to ensure all ingress and egress traffic is forcibly steered through the VCE’s LAN interface before proceeding to workload subnets or external networks.
   3.	Security Enforcement: Providing essential security functions, including IDS/IPS protection using the Suricata engine, managed by Arista's curated security signatures.


## Architecture diagram
{: #architecture-diagram}

Production environments mandate resilience against infrastructure failure, requiring an HA architecture across multiple IBM VPC availability zones.

![VeloCloud Virtual Edge deployed in HA configuration.](images/VeloCloud-SDWAN-HA-Architecture.svg "VeloCloud Virtual Edge deployed in HA configuration"){: caption="VeloCloud Virtual Edge deployed in HA configuration." caption-side="bottom"}
