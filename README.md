# Zabbix Network Monitoring Project

A network monitoring lab built with **Zabbix 8.0.0**, **GNS3**, **Cisco IOS routers**, and **VirtualBox**. The project demonstrates how to monitor network-device availability, interfaces, traffic, CPU, memory, and network errors using SNMP and ICMP.

## 1. Project Objectives

- Build a simulated multi-router network in GNS3.
- Connect the GNS3 lab to a Zabbix monitoring server.
- Configure SNMPv2c on Cisco routers.
- Add network devices to Zabbix using the **Cisco IOS by SNMP** template.
- Monitor device availability, interface status, bandwidth, CPU, memory, packet errors, and latency.
- Practise troubleshooting routing, DNS, SNMP, and monitoring-item issues.

## 2. Technologies and Environment

| Component | Technology / Version |
|---|---|
| Host operating system | Windows 11 |
| Virtualization | Oracle VirtualBox |
| Network simulator | GNS3 |
| Monitoring platform | Zabbix Appliance |
| Zabbix version | 8.0.0 |
| Zabbix appliance operating system | Rocky Linux 10.2 |
| Network devices | Cisco IOS routers and Ethernet switches |
| Monitoring protocols | SNMPv2c and ICMP |

## 3. Network Topology

The lab was expanded from an initial Cloud1–R1 connection into a multi-router topology.

**Devices included:**

- `Cloud1` – Connects the GNS3 topology to the Windows host-only network.
- `R1` – Main router and connection point for the other routers.
- `R2` – Additional router connected to R1.
- `R3` – Additional router connected to R1.
- `Switch1` / `Switch2` – Layer 2 switching devices in the lab.
- `PC1`–`PC4` – VPCS end devices.
- Zabbix Appliance – Central monitoring server running in VirtualBox.

### Logical topology overview

```text
                         Windows 11 Host
                               |
                            Cloud1
                               |
                         R1 (Main Router)
                         /             \
                    R1 -- R2         R1 -- R3
                     |                   |
                LAN / Switches       LAN / Switches
                  |      |             |      |
                 PC1    PC2           PC3    PC4

                   Zabbix Appliance
                 VirtualBox Host-only
                    192.168.56.102
```

> This is a logical overview. The exact switch-to-router and PC-to-switch connections depend on the saved GNS3 project.

## 4. IP Addressing and Routing

### Management network

| Device | Address | Purpose |
|---|---|---|
| Windows VirtualBox Host-only adapter | `192.168.56.1/24` | Host-side management connectivity |
| R1 | `192.168.56.10/24` | Router management/reachability |
| Zabbix Appliance (`eth1`) | `192.168.56.102/24` | Zabbix web and monitoring network |
| Zabbix Appliance (`eth0`) | `10.0.2.15` | VirtualBox NAT connectivity |

### Point-to-point router links

| Link | Device interface | IP address |
|---|---|---|
| R1–R2 (`10.12.0.0/30`) | R1 `g2/0` | `10.12.0.1/30` |
| R1–R2 (`10.12.0.0/30`) | R2 `g1/0` | `10.12.0.2/30` |
| R1–R3 (`10.13.0.0/30`) | R1 `g3/0` | `10.13.0.1/30` |
| R1–R3 (`10.13.0.0/30`) | R3 `g1/0` | `10.13.0.2/30` |

### LAN networks

| Network | Purpose |
|---|---|
| `192.168.20.0/24` | R2-side DHCP/LAN subnet |
| `192.168.30.0/24` | R3-side DHCP/LAN subnet |

### Routing configuration summary

- R2 uses a default route toward R1.
- R3 uses a default route toward R1.
- R1 has return routes for the R2 and R3 LAN networks.
- Routes were added on the Zabbix server toward the router networks so that the monitoring server can reach the remote devices.

The exact next-hop commands depend on the interface addressing in the saved GNS3 project. Verify the active routing tables with `show ip route` on Cisco devices and `ip route` on Rocky Linux.

## 5. Zabbix Server Setup

The Zabbix Appliance was installed in VirtualBox and accessed through its web interface.

- Web interface: `http://192.168.56.102`
- Host-only address: `192.168.56.102`
- NAT address: `10.0.2.15`
- Operating system: Rocky Linux 10.2
- Zabbix version: 8.0.0

The Host-only interface provides connectivity between the Windows host/GNS3 lab and the Zabbix server. The NAT interface provides outbound connectivity for repository access and updates.

## 6. SNMP Configuration on Cisco Routers

SNMPv2c was configured on the Cisco routers to allow Zabbix to collect device information.

Example configuration used:

```cisco
configure terminal
snmp-server community ZabbixRO RW
snmp-server location GNS3-LAB
snmp-server contact Network-Administrator
end
write memory
```

SNMP settings:

| Setting | Value |
|---|---|
| SNMP version | SNMPv2c |
| Community string | `ZabbixRO` |
| Permission configured in lab | RW |
| UDP port | `161` |
| Location | `GNS3-LAB` |
| Contact | `Network-Administrator` |

SNMP packet counters and Response PDUs were checked on R2 to verify that SNMP requests were reaching the router and receiving responses.

**Security note:** The lab uses an SNMP community configured with `RW` permissions. This allows management changes through SNMP and should only be used in an isolated lab. For production environments, use read-only access where possible, restrict SNMP access to trusted monitoring addresses, and consider SNMPv3.

## 7. Devices Added to Zabbix

The following hosts were added or configured in Zabbix.

| Zabbix host | SNMP / agent interface | Template / role |
|---|---|---|
| Cisco Router R1 | `192.168.56.10:161` | Cisco IOS by SNMP |
| Cisco Router R2 | `10.12.0.2:161` | Cisco IOS by SNMP |
| Cisco Router R3 | `10.13.0.2:161` | Cisco IOS by SNMP |
| Zabbix server | `127.0.0.1:10050` | Zabbix server / agent |

Common host configuration:

- Host group: `Network devices`
- Router template: `Cisco IOS by SNMP`
- SNMP version: SNMPv2c
- SNMP community macro: `{$SNMP_COMMUNITY}`
- Macro value: `ZabbixRO`

## 8. Monitoring Implemented and Investigated

The project explored the following monitoring areas:

| Monitoring area | What it shows |
|---|---|
| ICMP availability | Whether a router responds to ping |
| Interface status | Operational state of router interfaces |
| Interface traffic | Incoming and outgoing network traffic |
| Bandwidth utilisation | Traffic levels on network links |
| CPU utilisation | Router processor usage |
| Memory utilisation | Router memory usage |
| Packet errors and discards | Interface/network errors and dropped packets |
| Latency and packet loss | Network response time and reachability |

### Troubleshooting and validation

- Generated CPU load on R2 to observe CPU monitoring.
- Investigated a duplicate `icmpping` item-key conflict when linking the **ICMP Ping** template alongside **Cisco IOS by SNMP**.
- Reviewed the Zabbix **Latest data** page and the `With data` / `Without data` filters to distinguish items that had received values from items that had not yet collected data.
- Verified SNMP packet input/output counters and Response PDUs on R2.

## 9. Useful Troubleshooting Commands

### Cisco IOS

```cisco
show ip interface brief
show ip route
show running-config | include snmp
show snmp
ping 192.168.56.102
```

### Rocky Linux / Zabbix Appliance

```bash
ip addr
ip route
cat /etc/resolv.conf
ping 8.8.8.8
ping google.com
```

If `net-snmp-utils` is installed, test the router's SNMP system information with:

```bash
snmpwalk -v2c -c ZabbixRO 192.168.56.10 1.3.6.1.2.1.1
```

The command should return system information such as system description, object ID, uptime, name, location, and contact.

If `snmpwalk` is unavailable on Rocky Linux, install the `net-snmp-utils` package after confirming DNS and repository connectivity:

```bash
dnf clean all
dnf makecache --refresh
dnf install net-snmp-utils -y
```

## 10. Project Status and Future Improvements

The lab has progressed from basic Zabbix installation and single-router connectivity to a multi-router SNMP monitoring environment.

Possible next improvements:

- Confirm that all router hosts remain available and are collecting SNMP data.
- Complete SNMP walk testing from the Zabbix server.
- Add and monitor supported switches using suitable SNMP templates.
- Verify interface discovery and traffic graphs for each router.
- Configure triggers and alert notifications for device-down events, high CPU, high memory, and interface problems.
- Create a dashboard showing the whole lab's availability and performance.
- Document the final GNS3 topology and save configuration backups.
- Improve SNMP security by moving to read-only community access or SNMPv3.

## 11. Learning Outcomes

Through this project, the following practical skills were developed:

- Installing and accessing a Zabbix monitoring appliance.
- Configuring VirtualBox NAT and Host-only networking.
- Connecting GNS3 network devices to a host network.
- Configuring Cisco router interfaces and IP addressing.
- Implementing static routing between multiple routers.
- Configuring DHCP LANs and testing end-to-end connectivity.
- Enabling and validating SNMPv2c on Cisco IOS.
- Registering network devices in Zabbix and applying monitoring templates.
- Investigating monitoring-item conflicts and missing data.
- Understanding network availability, performance metrics, and troubleshooting workflows.

---

**Project:** Zabbix-Based Network Monitoring with GNS3  
**Environment:** Windows 11, VirtualBox, GNS3, Rocky Linux 10.2, Zabbix 8.0.0  
**Monitoring protocols:** SNMPv2c and ICMP
