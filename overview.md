---

copyright:
  years: 2026
lastupdated: "2026-01-02"


subcollection: pattern-network-vpc-sdwan
keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

There is a growing focus on deploying a SD-WAN for hybrid cloud solutions. SD-WAN offers better performance, lower costs, and simplified management by intelligently routing traffic across multiple connections (MPLS, broadband, LTE) for critical apps, providing centralized control, enhancing security with application-aware policies, and increasing visibility, all while boosting reliability and cutting expensive MPLS dependency. It ensures seamless, secure access to on-premise and cloud resources, crucial for modern, distributed workloads.

This deployment pattern looks a one specific SD-WAN virtual appliance, Arista VeloCloud SD-WAN Virtual Edge (VCE), presenting the network architecture connecting cloud workloads in IBM Cloud Virtual Private Cloud (VPC) and workloads in various on-premises branches and other hyperscalers.

The deployment of VCE within IBM Cloud VPC environments requires a meticulous, cloud-native architectural approach to ensure maximum performance, resilience, and scalability. Following Arista Networks' acquisition of the VeloCloud portfolio, the VCE functions as a core component of Arista's client-to-cloud networking strategy. It serves as a critical network virtual appliance (NVA) responsible for establishing the Dynamic Multi-Path Optimization (DMPO) overlay, securing branch-to-cloud communications, and enforcing security policies across enterprise assets.
This deployment pattern for automating VCE deployment, covers standalone, highly available (HA) multi-zone, and multi-region architectures within the IBM VPC framework.
