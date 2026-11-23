
# Zabbix Network Monitoring Project – Setup Tasks

## Phase 1 – Zabbix Server Setup

- [ ] Install and start Zabbix Appliance in VirtualBox.
- [ ] Access the Zabbix web interface.
- [ ] Change the default administrator password.
- [ ] Configure the Zabbix server IP address.
- [ ] Verify connectivity between Windows, GNS3 and Zabbix.
- [ ] Configure DNS on the Zabbix appliance.
- [ ] Verify internet connectivity using `ping 8.8.8.8` and `ping google.com`.

## Phase 2 – GNS3 Network Topology

- [ ] Create two Cisco routers (R1 and R2).
- [ ] Configure IP addresses on router interfaces.
- [ ] Configure DHCP on R1 and R2 if required.
- [ ] Configure routing between networks.
- [ ] Connect a switch to each router.
- [ ] Add PCs or VPCS devices.
- [ ] Connect the GNS3 topology to the Zabbix Appliance network.
- [ ] Verify connectivity using ping.

## Phase 3 – Add Network Devices to Zabbix

- [ ] Enable SNMP on Cisco R1.
- [ ] Enable SNMP on Cisco R2.
- [ ] Configure SNMP community strings.
- [ ] Verify SNMP connectivity from the Zabbix server.
- [ ] Add R1 as a host in Zabbix.
- [ ] Add R2 as a host in Zabbix.
- [ ] Configure SNMP interfaces for both routers.
- [ ] Assign appropriate Cisco SNMP templates.
- [ ] Verify that both routers show as available in Zabbix.

## Phase 4 – Monitor Network Performance

- [ ] Monitor router availability (ICMP Ping).
- [ ] Monitor interface status (up/down).
- [ ] Monitor interface traffic (incoming/outgoing).
- [ ] Monitor bandwidth utilization.
- [ ] Monitor CPU utilization.
- [ ] Monitor memory utilization.
- [ ] Monitor packet errors and discards.
- [ ] Monitor network latency and packet loss.

## Phase 5 – Configure Alerts and Triggers

- [ ] Create a router unreachable trigger.
- [ ] Create an interface-down trigger.
- [ ] Configure high CPU utilization alerts.
- [ ] Configure high memory utilization alerts.
- [ ] Configure packet loss alerts.
- [ ] Configure email notifications.
- [ ] Test alerts by disconnecting a router interface.
- [ ] Verify that Zabbix detects and reports the problem.

## Phase 6 – Dashboards and Visualization

- [ ] Create a Network Monitoring Dashboard.
- [ ] Add R1 and R2 availability widgets.
- [ ] Add network traffic graphs.
- [ ] Add CPU and memory utilization graphs.
- [ ] Add network problem widgets.
- [ ] Add interface status widgets.
- [ ] Display network availability statistics.
- [ ] Create a network topology map in Zabbix.

## Phase 7 – Advanced Monitoring

- [ ] Configure network device discovery.
- [ ] Configure automatic host registration where supported.
- [ ] Monitor additional switches.
- [ ] Add a Linux server for agent-based monitoring.
- [ ] Monitor disk usage and system resources.
- [ ] Configure SNMP-based device discovery.
- [ ] Configure network capacity monitoring.
- [ ] Test monitoring during network failures.

## Suggested Network Devices

| Device | Purpose | Monitoring Method |
|---|---|---|
| Zabbix Appliance | Monitoring server | Zabbix Server |
| R1 | Main router | SNMP + ICMP |
| R2 | Secondary router | SNMP + ICMP |
| SW1 | Network switch | SNMP |
| SW2 | Network switch | SNMP |
| PC1 | End device | ICMP |
| PC2 | End device | ICMP |
| Linux Server | Server monitoring | Zabbix Agent |

## Final Project Checklist

- [ ] Complete Zabbix Appliance installation.
- [ ] Complete GNS3 router configuration.
- [ ] Verify router connectivity.
- [ ] Configure SNMP.
- [ ] Add R1 and R2 to Zabbix.
- [ ] Monitor interfaces and network traffic.
- [ ] Configure alerts and triggers.
- [ ] Create monitoring dashboards.
- [ ] Expand the network topology.
- [ ] Perform final testing.
- [ ] Document the complete project.
