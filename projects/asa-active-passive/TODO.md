# Cisco ASA Active/Passive Lab - Completion To-Do List

## Priority 1: Critical Configurations (Must Complete)

### 1. ASA Secondary (ASA-SEC) Configuration
**Status:** 🔴 CRITICAL - Not Started  
**Estimated Time:** 30-45 minutes

#### Tasks:
- [ ] **Complete basic ASA-SEC failover configuration**
  ```cisco
  ! Set as secondary unit
  failover lan unit secondary
  
  ! Configure failover links (must match primary)
  failover lan interface folink g0/0
  failover interface ip folink 172.16.16.1 255.255.255.252 standby 172.16.16.2
  interface g0/0
   no shutdown
  
  failover interface ip statelink 172.16.16.5 255.255.255.252 standby 172.16.16.6
  interface g0/1
   no shutdown
  
  ! Enable failover
  failover
  ```

- [ ] **Verify interface configuration sync**
  - All interfaces should auto-sync from primary
  - Verify standby IP addresses are correct
  - Check that secondary ASA shows "Standby Ready" status

#### Verification Commands:
```cisco
show failover
show failover history
show interface ip brief
show route
```

### 2. Switch Base Configuration (SW1, SW2, SW3, SW4)
**Status:** 🔴 CRITICAL - Not Started  
**Estimated Time:** 2-3 hours

#### SW1 Configuration (Root Bridge for VLANs 10, 30):
- [ ] **Basic switch configuration**
  ```cisco
  hostname SW1
  enable secret cisco
  username admin privilege 15 password cisco
  
  ! Configure VTP
  vtp domain COMPANY
  vtp mode server
  vtp password cisco123
  
  ! Create VLANs
  vlan 10
   name MANAGEMENT
  vlan 20
   name HOSTS1
  vlan 30
   name HOSTS2
  vlan 40
   name HOSTS3
  ```

- [ ] **Interface configuration**
  ```cisco
  ! Trunk to ASA-PRI
  interface GigabitEthernet0/0
   description TRUNK-TO-ASA-PRI
   switchport trunk encapsulation dot1q
   switchport mode trunk
   switchport trunk allowed vlan 10,20,30,40
   no shutdown
  
  ! Trunk to SW2
  interface GigabitEthernet0/1
   description TRUNK-TO-SW2
   switchport trunk encapsulation dot1q
   switchport mode trunk
   switchport trunk allowed vlan 10,20,30,40
   spanning-tree link-type point-to-point
   no shutdown
  
  ! Access ports to SW3 and SW4
  interface GigabitEthernet0/5
   description TO-SW3
   switchport mode access
   switchport access vlan 10
   no shutdown
  
  interface GigabitEthernet0/6
   description TO-SW4
   switchport mode access
   switchport access vlan 30
   no shutdown
  ```

- [ ] **VLAN interfaces and HSRP**
  ```cisco
  ! VLAN 10 - Management (Active on SW1)
  interface Vlan10
   ip address 10.1.10.2 255.255.255.0
   standby 10 ip 10.1.10.1
   standby 10 priority 110
   standby 10 preempt
   no shutdown
  
  ! VLAN 20 - Hosts1 (Standby on SW1)
  interface Vlan20
   ip address 10.1.20.2 255.255.255.0
   standby 20 ip 10.1.20.1
   standby 20 priority 100
   no shutdown
  
  ! VLAN 30 - Hosts2 (Active on SW1)
  interface Vlan30
   ip address 10.1.30.2 255.255.255.0
   standby 30 ip 10.1.30.1
   standby 30 priority 110
   standby 30 preempt
   no shutdown
  
  ! VLAN 40 - Hosts3 (Standby on SW1)
  interface Vlan40
   ip address 10.1.40.2 255.255.255.0
   standby 40 ip 10.1.40.1
   standby 40 priority 100
   no shutdown
  ```

- [ ] **Spanning Tree configuration**
  ```cisco
  ! Enable MST
  spanning-tree mode mst
  spanning-tree mst configuration
   name COMPANY
   revision 1
   instance 1 vlan 10,30
   instance 2 vlan 20,40
   exit
  
  ! Set SW1 as root for instance 1
  spanning-tree mst 1 priority 8192
  spanning-tree mst 2 priority 16384
  ```

- [ ] **EIGRP configuration**
  ```cisco
  router eigrp 10
   network 10.1.10.0 0.0.0.255
   network 10.1.20.0 0.0.0.255
   network 10.1.30.0 0.0.0.255
   network 10.1.40.0 0.0.0.255
   network 10.1.1.16 0.0.0.3
   no auto-summary
  ```

#### SW2 Configuration (Root Bridge for VLANs 20, 40):
- [ ] **Basic configuration** (similar to SW1)
- [ ] **Interface configuration** (mirror SW1 with appropriate descriptions)
- [ ] **VLAN interfaces and HSRP** (opposite priorities from SW1)
  ```cisco
  ! VLAN 10 - Management (Standby on SW2)
  interface Vlan10
   ip address 10.1.10.3 255.255.255.0
   standby 10 ip 10.1.10.1
   standby 10 priority 100
   no shutdown
  
  ! VLAN 20 - Hosts1 (Active on SW2)
  interface Vlan20
   ip address 10.1.20.3 255.255.255.0
   standby 20 ip 10.1.20.1
   standby 20 priority 110
   standby 20 preempt
   no shutdown
  
  ! VLAN 30 - Hosts2 (Standby on SW2)
  interface Vlan30
   ip address 10.1.30.3 255.255.255.0
   standby 30 ip 10.1.30.1
   standby 30 priority 100
   no shutdown
  
  ! VLAN 40 - Hosts3 (Active on SW2)
  interface Vlan40
   ip address 10.1.40.3 255.255.255.0
   standby 40 ip 10.1.40.1
   standby 40 priority 110
   standby 40 preempt
   no shutdown
  ```

- [ ] **Spanning Tree configuration**
  ```cisco
  ! Set SW2 as root for instance 2
  spanning-tree mst 1 priority 16384
  spanning-tree mst 2 priority 8192
  ```

#### SW3 Configuration (Access Switch):
- [ ] **Basic Layer 2 configuration**
  ```cisco
  hostname SW3
  
  ! Uplink to SW1
  interface GigabitEthernet0/0
   description UPLINK-TO-SW1
   switchport mode access
   switchport access vlan 10
   no shutdown
  
  ! Host connections
  interface range GigabitEthernet3/1
   description MGMNT-HOST
   switchport mode access
   switchport access vlan 10
   no shutdown
  
  interface range GigabitEthernet3/2
   description VLAN20-HOST  
   switchport mode access
   switchport access vlan 20
   no shutdown
  ```

#### SW4 Configuration (Access Switch):
- [ ] **Basic Layer 2 configuration**
  ```cisco
  hostname SW4
  
  ! Uplink to SW2
  interface GigabitEthernet0/0
   description UPLINK-TO-SW2
   switchport mode access
   switchport access vlan 30
   no shutdown
  
  ! Host connections
  interface range GigabitEthernet3/1
   description VLAN30-HOST
   switchport mode access
   switchport access vlan 30
   no shutdown
  
  interface range GigabitEthernet3/2
   description VLAN40-HOST
   switchport mode access
   switchport access vlan 40
   no shutdown
  ```

### 3. ISP Router Configuration
**Status:** 🟡 NEEDS COMPLETION - Partially Done  
**Estimated Time:** 30 minutes

#### ISP1 Router:
- [ ] **Complete configuration**
  ```cisco
  hostname ISP1
  
  interface GigabitEthernet0/0
   description TO-ASA-PRI-OUTSIDE
   ip address 105.240.183.86 255.255.255.248
   no shutdown
  
  interface GigabitEthernet0/1
   description TO-ASA-SEC-OUTSIDE
   ip address 105.240.183.86 255.255.255.248
   no shutdown
  
  interface GigabitEthernet0/3
   description TO-INTERNET-SIM
   ip address dhcp
   no shutdown
  
  ! Static routes
  ip route 0.0.0.0 0.0.0.0 dhcp
  ip route 10.0.0.0 255.0.0.0 105.240.183.81
  ```

#### ISP2 Router:
- [ ] **Complete configuration**
  ```cisco
  hostname ISP2
  
  interface GigabitEthernet0/0
   description TO-ASA-PRI-OUTSIDE2
   ip address 64.173.45.166 255.255.255.248
   no shutdown
  
  interface GigabitEthernet0/1
   description TO-ASA-SEC-OUTSIDE2  
   ip address 64.173.45.166 255.255.255.248
   no shutdown
  
  interface GigabitEthernet0/3
   description TO-INTERNET-SIM
   ip address dhcp
   no shutdown
  
  ! Static routes
  ip route 0.0.0.0 0.0.0.0 dhcp
  ip route 10.0.0.0 255.0.0.0 64.173.45.161
  ```

## Priority 2: Host Device Configuration

### 4. VPCS Host Configuration
**Status:** 🟡 NEEDS COMPLETION  
**Estimated Time:** 20 minutes

#### MGMNT Host (VLAN 10):
- [ ] **Configure network settings**
  ```
  ip 10.1.10.100/24 10.1.10.1
  save
  ```

#### VLAN20 Host:
- [ ] **Configure network settings**
  ```
  ip 10.1.20.100/24 10.1.20.1
  save
  ```

#### VLAN30 Host:
- [ ] **Configure network settings**
  ```
  ip 10.1.30.100/24 10.1.30.1
  save
  ```

#### VLAN40 Host:
- [ ] **Configure network settings**
  ```
  ip 10.1.40.100/24 10.1.40.1
  save
  ```

## Priority 3: Testing and Verification

### 5. Connectivity Testing
**Status:** 🔴 NOT STARTED  
**Estimated Time:** 1-2 hours

#### Basic Connectivity Tests:
- [ ] **Ping tests from each VLAN to gateway**
  - Test MGMNT (10.1.10.100) → 10.1.10.1
  - Test VLAN20 (10.1.20.100) → 10.1.20.1
  - Test VLAN30 (10.1.30.100) → 10.1.30.1
  - Test VLAN40 (10.1.40.100) → 10.1.40.1

- [ ] **Inter-VLAN connectivity tests**
  - Test communication between VLANs through ASA
  - Verify security policies are working

- [ ] **Internet connectivity tests**
  - Test NAT translations
  - Verify external connectivity through both ISPs

#### HSRP/MHSRP Testing:
- [ ] **HSRP Status verification**
  ```cisco
  show standby brief
  show standby 10 detail
  show standby 20 detail
  show standby 30 detail
  show standby 40 detail
  ```

- [ ] **HSRP failover testing**
  - Shutdown active HSRP router for each VLAN
  - Verify standby takes over
  - Test host connectivity during failover

### 6. ASA Failover Testing
**Status:** 🔴 NOT STARTED  
**Estimated Time:** 1 hour

#### Failover Tests:
- [ ] **Check initial failover status**
  ```cisco
  show failover
  show failover history
  ```

- [ ] **Primary ASA failure simulation**
  - Power off ASA-PRI
  - Verify ASA-SEC becomes active
  - Test connectivity from all VLANs
  - Power on ASA-PRI and verify it becomes standby

- [ ] **Failover link testing**
  - Disconnect failover link
  - Verify split-brain detection
  - Reconnect and verify synchronization

- [ ] **State link testing**
  - Monitor connection table sync
  - Generate traffic and verify session preservation

### 7. Routing Protocol Testing
**Status:** 🔴 NOT STARTED  
**Estimated Time:** 30 minutes

#### EIGRP Verification:
- [ ] **Check EIGRP neighbors**
  ```cisco
  show ip eigrp neighbors
  show ip eigrp topology
  show ip route eigrp
  ```

- [ ] **Route redistribution verification**
  - Verify static routes are redistributed into EIGRP
  - Check routing tables on switches

## Priority 4: Documentation and Optimization

### 8. Performance Monitoring
**Status:** 🔴 NOT STARTED  
**Estimated Time:** 45 minutes

#### Monitoring Setup:
- [ ] **Configure logging on all devices**
  ```cisco
  logging buffered 50000
  logging console warnings
  logging monitor informational
  ```

- [ ] **Setup SNMP monitoring (optional)**
  ```cisco
  snmp-server community public ro
  snmp-server location "Lab Environment"
  snmp-server contact "lab-admin"
  ```

- [ ] **Performance baseline testing**
  - Generate traffic between VLANs
  - Monitor ASA CPU and memory usage
  - Test throughput between different paths

### 9. Security Policy Refinement
**Status:** 🟡 NEEDS IMPROVEMENT  
**Estimated Time:** 1 hour

#### Enhanced Security Policies:
- [ ] **Refine access control lists**
  - Create more granular rules per VLAN
  - Implement deny logging for troubleshooting

- [ ] **Configure object groups for better management**
  ```cisco
  object-group network INSIDE_NETWORKS
   network-object 10.1.10.0 255.255.255.0
   network-object 10.1.20.0 255.255.255.0
   network-object 10.1.30.0 255.255.255.0
   network-object 10.1.40.0 255.255.255.0
  ```

- [ ] **Implement application inspection**
  ```cisco
  class-map inspection_default
   match default-inspection-traffic
  policy-map global_policy
   class inspection_default
    inspect dns
    inspect http
    inspect https
    inspect icmp
    inspect ftp
  ```

### 10. Backup and Recovery
**Status:** 🔴 NOT STARTED  
**Estimated Time:** 30 minutes

#### Configuration Backup:
- [ ] **Save all device configurations**
  ```cisco
  copy running-config startup-config
  copy running-config tftp://backup-server/device-name.cfg
  ```

- [ ] **Document configuration differences**
  - Create comparison between primary and secondary ASA
  - Document HSRP priority assignments
  - Create network diagram with current IP assignments

- [ ] **Create recovery procedures**
  - Document steps to recover from complete ASA failure
  - Create switch stack recovery procedures
  - Document VLAN recreation steps

## Summary Checklist

### Critical Path Items (Must Complete First):
1. ✅ ASA-SEC failover configuration
2. ✅ SW1 and SW2 complete configuration  
3. ✅ Basic connectivity testing
4. ✅ HSRP verification
5. ✅ ASA failover testing

### Secondary Items (Complete After Critical Path):
6. ⭕ SW3 and SW4 access switch configuration
7. ⭕ ISP router completion
8. ⭕ Host device configuration
9. ⭕ Performance monitoring setup
10. ⭕ Security policy refinement

### Documentation Items (Ongoing):
11. ⭕ Configuration backups
12. ⭕ Network diagram updates
13. ⭕ Test results documentation
14. ⭕ Troubleshooting guide creation

## Estimated Total Completion Time: 8-12 hours

## Success Criteria

The lab will be considered complete when:
- [ ] Both ASA units are in active/standby failover state
- [ ] All VLANs can communicate through ASA with proper security policies
- [ ] HSRP is working correctly for all VLANs with proper load balancing
- [ ] Failover scenarios work correctly (ASA and HSRP)
- [ ] All hosts can reach the internet through both ISP connections
- [ ] EIGRP is converging properly across all devices
- [ ] All configurations are saved and documented

## Next Steps After Completion

Once the basic lab is working:
1. **Add advanced features:**
   - Site-to-site VPN tunnels
   - Advanced threat protection
   - QoS policies
   - Remote access VPN

2. **Create additional test scenarios:**
   - Disaster recovery testing
   - Performance under load
   - Security penetration testing
   - Compliance validation

3. **Documentation:**
   - Create user guides
   - Build troubleshooting runbooks
   - Develop training materials
   - Document lessons learned