# Cisco ASA Active/Passive Failover Lab

## Overview

This GNS3 laboratory demonstrates a comprehensive Cisco ASA firewall high availability setup using active/passive failover configuration. The lab includes multiple network segments, redundant internet connections, and advanced switching configurations with HSRP/MHSRP for gateway redundancy.

## Lab Objectives

By completing this lab, you will learn:

1. **ASA Failover Configuration**
   - Configure stateful failover between two ASA firewalls
   - Implement dedicated failover and state link connections
   - Test failover scenarios and recovery procedures

2. **Network Redundancy**
   - Configure HSRP/MHSRP for gateway redundancy
   - Implement load balancing across multiple internet connections
   - Design resilient network architecture

3. **Routing Integration**
   - Configure EIGRP between ASAs and internal network
   - Implement route redistribution
   - Configure backup routes with administrative distance

4. **Security Policies**
   - Configure zone-based security policies
   - Implement NAT for multiple inside networks
   - Configure access control lists

## Network Topology

```
                     INTERNET
                        |
              ┌─────────┴─────────┐
              │                   │
         ┌────▼────┐         ┌────▼────┐
         │  ISP1   │         │  ISP2   │
         │105.240. │         │64.173.  │
         │183.86/29│         │45.166/29│
         └────┬────┘         └────┬────┘
              │                   │
              │                   │
       ┌──────▼──────┐     ┌──────▼──────┐
       │   ASA-PRI   │ === │   ASA-SEC   │
       │ (Primary)   │     │ (Secondary) │
       │             │     │             │
       └──────┬──────┘     └──────┬──────┘
              │                   │
              │   INSIDE NETWORK  │
              └─────────┬─────────┘
                        │
            ┌───────────▼───────────┐
            │                       │
       ┌────▼────┐             ┌────▼────┐
       │   SW1   │             │   SW2   │
       │         │             │         │
       └────┬────┘             └────┬────┘
            │                       │
    ┌───────▼───────┐       ┌───────▼───────┐
    │               │       │               │
┌───▼───┐       ┌───▼───┐ ┌─▼──┐        ┌───▼───┐
│  SW3  │       │ MGMNT │ │SW4 │        │VLAN40 │
│       │       │VLAN10 │ │    │        │       │
└───┬───┘       └───────┘ └─┬──┘        └───────┘
    │                       │
┌───▼───┐               ┌───▼───┐
│VLAN20 │               │VLAN30 │
│       │               │       │
└───────┘               └───────┘
```

## IP Addressing Scheme

### ASA Interfaces
| Interface | Primary ASA | Secondary ASA | Standby IP | Description |
|-----------|-------------|---------------|------------|-------------|
| G0/2 (inside) | 10.1.1.1/29 | - | 10.1.1.2 | Primary inside network |
| G0/3 (inside2) | 10.1.1.9/29 | - | 10.1.1.10 | Secondary inside network |
| G0/5 (outside) | 105.240.183.81/29 | - | 105.240.183.82 | Primary ISP connection |
| G0/6 (outside2) | 64.173.45.161/29 | - | 64.173.45.162 | Secondary ISP connection |
| G0/0 (folink) | 172.16.16.1/30 | 172.16.16.2/30 | - | Failover link |
| G0/1 (statelink) | 172.16.16.5/30 | 172.16.16.6/30 | - | State synchronization |

### VLAN Configuration
| VLAN | Network | HSRP VIP | SW1 Priority | SW2 Priority | Purpose |
|------|---------|----------|--------------|--------------|---------|
| 10 | 10.1.10.0/24 | 10.1.10.1 | 110 (Active) | 100 (Standby) | Management |
| 20 | 10.1.20.0/24 | 10.1.20.1 | 100 (Standby) | 110 (Active) | Hosts 1 |
| 30 | 10.1.30.0/24 | 10.1.30.1 | 110 (Active) | 100 (Standby) | Hosts 2 |
| 40 | 10.1.40.0/24 | 10.1.40.1 | 100 (Standby) | 110 (Active) | Hosts 3 |

### ISP Connections
| ISP | Gateway | Network |
|-----|---------|---------|
| ISP1 | 105.240.183.86 | 105.240.183.80/29 |
| ISP2 | 64.173.45.166 | 64.173.45.160/29 |

## Current Configuration Status

### ✅ Completed Components
- [x] ASA Primary basic interface configuration
- [x] ASA Primary failover link configuration
- [x] ASA Primary NAT policies
- [x] ASA Primary access control lists
- [x] ASA Primary EIGRP configuration
- [x] ASA Primary routing configuration
- [x] Network topology design
- [x] IP addressing scheme
- [x] GNS3 project file structure

### 🔄 Partially Configured
- [ ] ASA Secondary complete configuration
- [ ] Switch configurations (SW1, SW2, SW3, SW4)
- [ ] HSRP/MHSRP configuration
- [ ] EIGRP routing on switches
- [ ] VLAN configurations
- [ ] Host device configurations

### ❌ Not Started
- [ ] Failover testing procedures
- [ ] Performance monitoring setup
- [ ] Backup and recovery procedures
- [ ] Security policy testing
- [ ] Load balancing verification

## Files in This Project

```
asa-active-passive/
├── README.md                    # This documentation
├── asa-active-passive.gns3      # GNS3 project file
├── configs/                     # Device configurations
│   ├── ASA-PRI.cfg             # Primary ASA configuration
│   ├── ASA-SEC.cfg             # Secondary ASA configuration
│   ├── ISP1.cfg                # ISP1 router configuration
│   ├── ISP2.cfg                # ISP2 router configuration
│   ├── SW1.cfg                 # Switch 1 configuration
│   ├── SW2.cfg                 # Switch 2 configuration
│   ├── SW3.cfg                 # Switch 3 configuration
│   └── SW4.cfg                 # Switch 4 configuration
├── project-files/              # GNS3 project data
└── results/                    # Test results and documentation
```

## Prerequisites

### Required Software
- GNS3 2.2.8 or later
- Cisco ASAv image (asav981.qcow2)
- Cisco IOSv image for routers
- Cisco IOSvL2 image for switches

### Required Images
| Device Type | Image File | Version | MD5 Checksum |
|-------------|------------|---------|--------------|
| ASAv | asav981.qcow2 | 9.8.1 | 8d3612fe22b1a7dec118010e17e29411 |
| IOSv Router | vios-adventerprisek9-m.vmdk.SPA.156-2.T | 15.6.2T | 83707e3cc93646da58ee6563a68002b5 |
| IOSvL2 Switch | vios_l2-adventerprisek9-m.vmdk.SSA.152-4.0.55.E | 15.2.4.0.55.E | 1a3a21f5697cae64bb930895b986d71e |

## Setup Instructions

### 1. GNS3 Project Import
1. Open GNS3
2. Import the project file: `File > Import portable project`
3. Select `asa-active-passive.gns3`
4. Verify all appliances are properly mapped to your images

### 2. Device Configuration
1. Start with ISP routers (ISP1, ISP2)
2. Configure switches (SW1, SW2, SW3, SW4)
3. Complete ASA-SEC configuration
4. Configure host devices (MGMNT, VLAN20, VLAN30, VLAN40)

### 3. Testing and Verification
1. Test basic connectivity between all segments
2. Verify HSRP operation on switches
3. Test ASA failover scenarios
4. Validate NAT translations
5. Confirm routing table propagation

## Configuration Examples

### ASA Secondary Configuration Template
```cisco
! Basic failover configuration for secondary ASA
failover lan unit secondary
failover lan interface folink g0/0
failover interface ip folink 172.16.16.1 255.255.255.252 standby 172.16.16.2
interface g0/0
 no shutdown

failover interface ip statelink 172.16.16.5 255.255.255.252 standby 172.16.16.6
interface g0/1
 no shutdown

failover
```

### Switch HSRP Configuration Template
```cisco
! HSRP configuration for VLAN 10 on SW1 (Active)
interface vlan 10
 ip address 10.1.10.2 255.255.255.0
 standby 1 ip 10.1.10.1
 standby 1 priority 110
 standby 1 preempt
 no shutdown
```

## Testing Procedures

### 1. Failover Testing
1. **Primary ASA Failure Simulation**
   - Shutdown primary ASA
   - Verify secondary takes over within 15 seconds
   - Test connectivity from all VLANs

2. **Link Failure Testing**
   - Disconnect outside interfaces
   - Verify backup routes activate
   - Test traffic flow through alternate paths

3. **Switch Failover Testing**
   - Shutdown active HSRP router per VLAN
   - Verify standby router becomes active
   - Test gateway functionality

### 2. Performance Testing
1. Generate traffic between VLANs
2. Monitor ASA resource utilization
3. Test NAT translation capacity
4. Verify routing convergence times

## Troubleshooting

### Common Issues
1. **Failover Not Working**
   - Check failover link connectivity
   - Verify state link configuration
   - Ensure matching failover keys

2. **HSRP Flapping**
   - Check switch-to-switch connectivity
   - Verify HSRP timers
   - Confirm preempt settings

3. **Routing Issues**
   - Verify EIGRP neighbor relationships
   - Check network statements
   - Confirm route redistribution

### Debug Commands
```cisco
! ASA Failover Debugging
show failover
show failover history
debug failover

! HSRP Debugging
show standby brief
debug standby events

! EIGRP Debugging
show ip eigrp neighbors
show ip route eigrp
debug eigrp packets
```

## Learning Resources

### Cisco Documentation
- [ASA Failover Configuration Guide](https://cisco.com/c/en/us/td/docs/security/asa/asa96/configuration/ha/asa-96-ha-config.html)
- [HSRP Configuration Guide](https://cisco.com/c/en/us/td/docs/ios-xml/ios/ipapp_fhrp/configuration/15-sy/fhp-15-sy-book.html)
- [EIGRP Configuration Guide](https://cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_eigrp/configuration/15-mt/ire-15-mt-book.html)

### Additional Labs
- Configure ASA clustering for active/active failover
- Implement route-based VPN between sites
- Add intrusion detection and prevention
- Configure advanced NAT policies

## Support

For questions or issues with this lab:
1. Check the troubleshooting section above
2. Review Cisco documentation links
3. Examine log files in the `results/` directory
4. Create an issue in the project repository

---

**Note:** This lab is designed for educational purposes. Ensure you have proper licensing for all Cisco images used in this laboratory environment.