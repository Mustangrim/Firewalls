# FortiOS Basic Firewall Policies Lab

## Lab Overview

**Lab Focus:** Enterprise firewall configuration and network security implementation using FortiGate VM  
**Difficulty:** Intermediate    
**Author:** Mykyta Palamarchuk  
**Role:** SOC Analyst | Incident Response Specialist  
**Certification:** CompTIA Security+ (SY0-701)

### Environment Specifications
- **Platform:** FortiGate VM (FortiOS)
- **Network Topology:** Three-segment architecture (WAN/LAN/DMZ)
- **Management Interface:** Web GUI + CLI access
- **Validation Method:** Traffic flow verification and log analysis

---

## Learning Objectives

Upon completion of this lab, participants will be able to:

1. **Configure network interfaces** with appropriate roles, aliases, and administrative access controls
2. **Implement Virtual IP address translation** for inbound service publishing from DMZ
3. **Create and manage firewall policies** controlling inter-zone traffic flows
4. **Apply NAT configurations** appropriately based on traffic direction and security requirements
5. **Validate security controls** through log analysis and traffic testing

---

## Lab Architecture

```
Internet (WAN) ←→ [FortiGate VM] ←→ Internal LAN
                        ↓
                      DMZ Network
                    (Web Server)
```

**Network Segments:**
- **WAN Interface (Port2):** External-facing interface for internet connectivity
- **LAN Interface (Port3):** Internal network for corporate users  
- **DMZ Interface (Port4):** Demilitarized zone hosting public services
- **Management Interface:** Administrative access (DO NOT MODIFY)

---

## Lab Exercises

### Exercise 1: Interface Configuration and Role Assignment

**Scenario:** Configure FortiGate VM interfaces with proper security zones and administrative access controls to establish foundational network segmentation.

#### Objective
Assign appropriate interface roles, descriptive aliases, and granular administrative access permissions to support the three-tier network architecture.

#### Task Requirements

**Access FortiGate VM:**
- URL: `https://fortigate.lab`
- Username: `student`  
- Password: `*******`

**Interface Configuration Tasks:**

1. **WAN Interface (Port2) Configuration:**
   - Alias: `WAN`
   - Role: `WAN`  
   - Administrative Access: `PING` (minimal access for troubleshooting)

2. **LAN Interface (Port3) Configuration:**
   - Alias: `LAN`
   - Role: `LAN`
   - Administrative Access: `HTTPS, HTTP, SSH, PING` (full management access)

3. **DMZ Interface (Port4) Configuration:**
   - Alias: `DMZ`
   - Role: `DMZ`
   - Administrative Access: `None` (no direct management from DMZ)

#### Implementation Steps

1. Navigate to **Network → Interfaces** in the FortiGate web console
2. Right-click on each interface and select **Edit**
3. Configure the specified parameters for each interface
4. Apply changes and verify configuration

#### Validation
- Verify interface roles are correctly assigned
- Confirm administrative access restrictions are in place
- Test management connectivity from appropriate network segments

![Interface Configuration](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/interface-configuration.png)

---

### Exercise 2: Virtual IP Configuration for Service Publishing

**Scenario:** Configure Virtual IP mappings to publish internal web services to external users while maintaining network segmentation and security.

#### Objective
Create Virtual IP address translations to redirect external HTTP/HTTPS requests from the WAN interface to the internal web server in the DMZ.

#### Technical Background
Virtual IPs in FortiOS function similarly to destination NAT in iptables, providing port forwarding capabilities for publishing internal services to external networks while maintaining security boundaries.

#### Task Requirements

**Create Virtual IP for HTTP Service:**
- Name: `web server - HTTP`
- Interface: `WAN (port2)`
- External IP Address: FortiGate WAN IP address
- Mapped IP Address: Web Server internal IP address  
- Protocol: `TCP`
- External Service Port: `80`
- Map to Port: `80`

**Create Virtual IP for HTTPS Service:**  
- Name: `web server - HTTPS`
- Interface: `WAN (port2)`
- External IP Address: FortiGate WAN IP address
- Mapped IP Address: Web Server internal IP address
- Protocol: `TCP`  
- External Service Port: `443`
- Map to Port: `443`

#### Implementation Steps

1. Navigate to **Policy & Objects → Virtual IPs**
2. Click **+ Create New** to add first Virtual IP
3. Configure HTTP Virtual IP with specified parameters
4. Create second Virtual IP for HTTPS service
5. Verify both Virtual IPs are correctly configured

![HTTP Virtual IP Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/virtual-ip-http-creation.png)

![HTTPS Virtual IP Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/virtual-ip-https-creation.png)

#### Security Considerations
- Virtual IPs handle address translation; additional firewall policies required for traffic flow
- Consider implementing intrusion prevention and application control for published services
- Monitor logs for unauthorized access attempts to published services

---

### Exercise 3: Firewall Policy Implementation

**Scenario:** Create comprehensive firewall policies to control traffic flow between network segments while maintaining security principles and operational requirements.

#### Objective
Implement three distinct firewall policies to enable controlled communication between WAN, LAN, and DMZ network segments with appropriate security controls.

---

#### Policy 1: External Access to DMZ Services (WAN → DMZ)

**Purpose:** Allow external users to access web services hosted in the DMZ through Virtual IP mappings.

**Policy Configuration:**
- Name: `WAN to DMZ`
- Incoming Interface: `WAN (port2)`
- Outgoing Interface: `DMZ (port4)`  
- Source: `all`
- Destination: `web server - HTTP, web server - HTTPS` (Virtual IPs)
- Schedule: `always`
- Service: `HTTP, HTTPS`
- Action: `Accept`
- **NAT: DISABLED** (handled by Virtual IPs)

**Security Rationale:** NAT is disabled because address translation is managed by the Virtual IP configuration. Enabling NAT would cause the web server to lose visibility of actual client source addresses, reducing security monitoring capabilities.

![WAN to DMZ Policy](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/wan-to-dmz-policy.png)

---

#### Policy 2: Internal Access to DMZ Services (LAN → DMZ)

**Purpose:** Enable internal users to access DMZ services using internal IP addresses for administrative and business purposes.

**Policy Configuration:**
- Name: `LAN to DMZ`  
- Incoming Interface: `LAN (port3)`
- Outgoing Interface: `DMZ (port4)`
- Source: `all`
- Destination: `all` (allows access to current and future DMZ services)
- Schedule: `always`
- Service: `HTTP, HTTPS, PING, SSH`
- Action: `Accept`
- **NAT: DISABLED** (preserves source IP visibility)

**Security Rationale:** SSH access is included for administrative purposes. NAT remains disabled to maintain audit trails and proper source IP logging for internal access monitoring.

![LAN to DMZ Policy](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/lan-to-dmz-policy.png)

---

#### Policy 3: Internet Access for Internal Users (LAN → WAN)

**Purpose:** Provide internet connectivity for internal users while implementing proper network address translation for return traffic routing.

**Policy Configuration:**
- Name: `LAN to WAN`
- Incoming Interface: `LAN (port3)`  
- Outgoing Interface: `WAN (port2)`
- Source: `all`
- Destination: `all`
- Schedule: `always` 
- Service: `ALL`
- Action: `Accept`
- **NAT: ENABLED** (required for internet access)

**Security Rationale:** NAT is essential for this policy as it replaces internal source addresses with the firewall's WAN IP, enabling proper routing of return traffic from internet services.

![LAN to WAN Policy](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/lan-to-wan-policy.png)

#### Implementation Steps for All Policies

1. Navigate to **Policy & Objects → Firewall Policy**
2. Click **+ Create New** for each policy
3. Configure parameters as specified above
4. Pay careful attention to NAT settings for each policy
5. Apply and verify each policy before proceeding

---

## Validation and Testing

### Traffic Flow Verification

**Recommended Testing Scenarios:**

1. **External Web Access Test:**
   - Access web server from external network using FortiGate WAN IP
   - Verify both HTTP and HTTPS connectivity
   - Confirm Virtual IP translations are functioning

2. **Internal DMZ Access Test:**
   - Access web server from LAN using internal DMZ IP addresses
   - Test SSH connectivity for administrative access
   - Verify ping functionality for network troubleshooting

3. **Internet Connectivity Test:**
   - Browse external websites from LAN clients
   - Confirm NAT is properly translating source addresses
   - Test various internet services and protocols

### Log Analysis

**Monitor traffic flows using:**
- **Log & Report → Forward Traffic** - Review policy matches and traffic patterns
- **Log & Report → System Events** - Monitor configuration changes and system status
- **Policy & Objects → Firewall Policy** - Verify policy hit counts and effectiveness

### Troubleshooting Common Issues

**Policy Order:** FortiGate processes policies top-to-bottom; ensure most specific policies are positioned above more general rules.

**Implicit Deny:** All unlisted traffic is denied by default; verify comprehensive policy coverage for required traffic flows.

**NAT Configuration:** Incorrect NAT settings can break return traffic routing or eliminate source IP visibility for security monitoring.

---

## Security Best Practices Applied

1. **Principle of Least Privilege:** Administrative access limited by interface and protocol
2. **Network Segmentation:** Clear separation between WAN, LAN, and DMZ segments  
3. **Service-Specific Policies:** Granular control over allowed services and protocols
4. **Audit Trail Preservation:** Strategic NAT usage maintains source IP visibility where possible
5. **Defense in Depth:** Multiple layers of control through interface roles, Virtual IPs, and firewall policies

---

## Advanced Extensions

**For additional learning, consider implementing:**

- **Application Control:** Identify and control specific applications within allowed traffic
- **Intrusion Prevention:** Deploy IPS signatures to detect and block malicious activity
- **SSL Inspection:** Deep packet inspection of encrypted traffic for advanced threat detection
- **User Authentication:** Identity-based access control integrated with Active Directory
- **High Availability:** Configure redundant FortiGate pairs for business continuity

---

## Lab Summary

This lab demonstrates foundational enterprise firewall configuration using FortiGate VM, covering interface management, service publishing through Virtual IPs, and comprehensive policy implementation. The exercises build practical skills in network security architecture, traffic flow control, and security monitoring that are essential for SOC analysts and security engineers.

**Key Takeaways:**
- Proper interface role assignment enhances security and simplifies management
- Virtual IPs provide secure service publishing without compromising network segmentation  
- Strategic NAT configuration balances functionality with security monitoring requirements
- Comprehensive logging enables effective incident response and security analysis

