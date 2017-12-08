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
> ESXi Initial Configuration:

Using a browser, Login to each ESXi https://<FQDN>, do the following:
1) Configure NTP
2) Configure Domain and Active Directory Authentication if needed.
3) License the esxi.
4) Enable SSH if needed.

![](https://i.imgur.com/MK0zGsK.png)

![](https://i.imgur.com/l6lxMg3.png)

![](https://i.imgur.com/kbOWGqF.png)

# vCenter Deployment
## vCenter High-Avability
![](https://i.imgur.com/1PUsHBp.png)

Check the below link:
[https://kb.vmware.com/s/article/2148442](https://kb.vmware.com/s/article/2148442 "Deploying vCenter High Availability with network addresses in separate subnets")
[https://www.altaro.com/vmware/how-to-deploy-a-vcenter-ha-cluster-part-1/](https://www.altaro.com/vmware/how-to-deploy-a-vcenter-ha-cluster-part-1/)
[https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.avail.doc/GUID-33AC12C8-EEB7-422D-831B-B1B5A7FECC44.html](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.avail.doc/GUID-33AC12C8-EEB7-422D-831B-B1B5A7FECC44.html)
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-overview-part-1/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-overview-part-1/)
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-external-psc-deployment-part-2/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-external-psc-deployment-part-2/)

![](https://i.imgur.com/hYnOjdo.png)
> Requirements:
> 1) vCSA 6.5. HA is only supported on this release
> 2) HA cluster nodes are only supported on ESXi 5.5 and/or vCenter Server 5.5 or later
> 3) Ideally, every HA node should reside on a different host and datastore. Optionally, the hosts should be in a DRS cluster. A minimum of 3 hosts is then required.
> 4) The vCSA deployment size should be set to Small (4 vCPUs / 16GB RAM) or better.
> 5) Create a port group on ESXi for the private HA network. Optionally, you can have a dedicated vSwitch if network isolation is a requirement.
> 5) The HA private network must reside on a different subnet other than that used for management.
> 6) You cannot mix IP4 and IP6 addressing when configuring networking on the nodes.
> 7) Network latency on the HA network must be less than 10ms.
> 8) No gateway for the HA network must be specified when configuring the nodes.
> 9) You will need one IP address for management and three private IP addresses, one for each HA node.
> 10) DNS A and PTR records for the Active node’s management network (i.e. FDQN for the vCSA).

### HA Network on ESXi
![](https://i.imgur.com/Dy78s39.png)


**Note:** Promiscuous mode must only be enabled on nested environments such as the one I’m using. **It is not** an vCSA HA requirement.

### External PSC " Platform Service Controller "
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-overview-part-1/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-overview-part-1/)
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-external-psc-deployment-part-2/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-external-psc-deployment-part-2/)
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-load-balancer-config-part-3/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-load-balancer-config-part-3/)
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-deploy-vcenter-part-4/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-deploy-vcenter-part-4/)
[https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-enable-vcenter-ha-part-5/](https://thewificable.com/2017/01/06/vcenter-6-5-high-availability-enable-vcenter-ha-part-5/)
[https://www.virtuallyghetto.com/2015/12/what-does-load-balancing-the-platform-services-controller-really-give-you.html](https://www.virtuallyghetto.com/2015/12/what-does-load-balancing-the-platform-services-controller-really-give-you.html)

**For a demo purpose, I will deploy vCenter Server with an Embedded PSC:**

![](https://i.imgur.com/qMnXMe8.png)

Check hostanme using the lookup command.

![](https://i.imgur.com/oxV937l.png)

![](https://i.imgur.com/VS0SRQA.png)

![](https://i.imgur.com/FqdPDsK.png)

![](https://i.imgur.com/QSbtH2f.png)

![](https://i.imgur.com/fsU38rR.png)

![](https://i.imgur.com/ghZmjz5.png)

![](https://i.imgur.com/YqZP0lC.png)

![](https://i.imgur.com/Fj05dAs.png)

![](https://i.imgur.com/OM4KqCe.png)

![](https://i.imgur.com/iIkz8p5.png)

![](https://i.imgur.com/CtIMcEe.png)

![](https://i.imgur.com/CMcXHK9.png)

vCenter was not reachable since the port group created is not reflecting the correct VLAN information: to be fixed 

![](https://i.imgur.com/Be7bURh.png)

![](https://i.imgur.com/F76FGdo.png)

Resume phase 2:

![](https://i.imgur.com/TwrEH30.png)

![](https://i.imgur.com/XpYGzfg.png)

![](https://i.imgur.com/2KhK3MP.png)

![](https://i.imgur.com/toHgpzh.png)

![](https://i.imgur.com/F0hnyV2.png)

![](https://i.imgur.com/dYThrtn.png)

![](https://i.imgur.com/0z4xS2w.png)

![](https://i.imgur.com/Jqm1zvz.png)

Will follow the same steps for the resource/edge cluster vcenter.

![](https://i.imgur.com/2GQhKXx.png)
