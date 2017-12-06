# VMware-vCloud-NFV2.0
Greenfield installation of a Vcloud NFV 2.0 using the following versions:
- VMware vCenter 6.5e
- VMware ESXi 6.5d
- VMware Virtual SAN 6.6
- VMware NSX 6.3.1
- VMware vRealize Orchestrator Appliance 7.2
- VMware vRealize Operations Manager 6.5
- VMware vRealize Log Insight 4.3
- vCloud Director for Service Providers 8.20

Outline the design considerations to implement Network Functions Virtualization Infrastructure (NFVI) platforms, which is built to accommodate service provider level requirements running on Dell EMC hardware.

## Getting Started

Through out this deployment exercise, we will show a setp-by-step deployment and management for all VMware above components. In addition to that, we will deploy Dell EMC ScaleIO to act as Software Defined Storage for the tenant cluster.

### Network Prerequisites
> Infrastructure network requirements: 

   Seq  | Name | Network Address | VLAN_ID | Switch | Tagged/Untagged | TYPE
------------- | ------------- | ------------- | ------------- | ------------- | ------------- | -------------
1 	| Ext-Mgmt | 172.17.84.0/24 | 3084  | mgmt/Global | Tagged | VMkernal
2   | vMotion-mgmt | 10.30.200.0/24 | 200 | ToR | Tagged | VMkernal
3  | vSAN | 10.30.300.0/24 | 300 | ToR | Tagged | VMkernal
4  | Replication | 10.30.400.0/24 | 400 | ToR | Tagged | VMkernal

> Workload network requirements:

   Seq  | Name | Network Address | VLAN_ID | Switch | Tagged/Untagged | TYPE
------------- | ------------- | ------------- | ------------- | ------------- | ------------- | -------------
1 	| VNF-Mgmt | 192.0.500.0/24 | 500  | ToR | Tagged | Virtual Machine
2   | VNF-Ext | 192.0.700.0/24 | 700 | ToR | Tagged | Virtual Machine
3  | VxLAN-Underlay | 10.0.600.0/24 | 600 | ToR | Tagged | VMkernal

# ESXi Deployment
Using the iDRAC and virtual CDROM:

![](https://i.imgur.com/iPgvyM3.png) 

![](https://i.imgur.com/RWl1ZDC.png)

![](https://i.imgur.com/UsTcsEx.png)

![](https://i.imgur.com/vVaJEMs.png)

![](https://i.imgur.com/8rr6ruw.png)

![](https://i.imgur.com/XN6LQ7k.png)

![](https://i.imgur.com/5MB3qZe.png)

![](https://i.imgur.com/pQbkUBN.png)

![](https://i.imgur.com/TB7RMdq.png)

After server deployment, There will be an initial configuration which require console access
1. Network Information, such as " NIC, IP Address, VLAN ID, GW and Netmask "
2. Hostname
3. Domain 
4. DNS Configuration:L Make sure that forward and reverse lookup is configured for all servers

