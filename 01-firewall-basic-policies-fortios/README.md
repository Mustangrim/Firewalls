# Firewall Basic Policies - FortiOS

## Lab Overview
This comprehensive lab explores enterprise FortiOS firewall configuration fundamentals, essential for SOC analysts and cybersecurity professionals managing network security infrastructure. You'll master interface configuration, Virtual IP setup, and firewall policy implementation critical for enterprise network segmentation, external service exposure, and secure internet access management in cybersecurity environments.

**Author:** Mykyta Palamarchuk  
**Role Focus:** SOC Analyst | Incident Response Specialist  
**Certification:** CompTIA Security+ (SY0-701)  
**Framework Alignment:** Network security and perimeter defense for cybersecurity operations

## Learning Objectives

- Master FortiOS interface configuration with security-focused role assignments for enterprise environments
- Implement Virtual IP configuration for secure external service exposure and advanced NAT concepts
- Configure comprehensive firewall policies supporting network segmentation and secure communications
- Apply enterprise-grade security controls through proper interface roles and administrative access restrictions
- Develop systematic approaches to firewall policy management for SOC operations and incident response
- Understand FortiGate VM administration through both GUI and CLI environments for cybersecurity professionals

## Introduction to Enterprise Firewall Management

### Understanding FortiOS in Security Operations

Fortinet provides intelligent and seamless protection technologies to more than 450,000 customers, including Fortune 500 companies, worldwide. FortiGate enterprise firewall platforms come in various sizes and form factors to accommodate customer needs and fit seamlessly into enterprise environments. Customers can acquire Next-Generation Firewall solutions as physical appliances or virtual machines supporting various virtualization technologies.

FortiOS serves as the operating system at the heart of both physical security devices and virtual machines, providing the foundation for enterprise network security operations.

### Firewall Policies in Cybersecurity Context

**Network Segmentation:** FortiGate policies enable secure network segmentation, isolating critical assets and controlling traffic flows between LAN, DMZ, and WAN zones essential for SOC operations.

**Incident Response:** Proper firewall configuration supports rapid incident containment and forensic access while maintaining security boundaries during security investigations.

**Compliance:** Enterprise firewall policies ensure regulatory compliance through controlled access, detailed logging, and comprehensive traffic monitoring capabilities.

**Threat Prevention:** Advanced firewall policies integrate with FortiGuard services to prevent malware, intrusions, and data exfiltration attempts in real-time.

## Interface Configuration

### Understanding Enterprise Interface Management

FortiGate VM offers users both CLI and WEB GUI environments. This lab focuses on the WEB GUI interface, but you have the liberty to perform all tasks via SSH command line interface if preferred. Proper interface configuration establishes the foundation for enterprise network security, defining network roles, access permissions, and administrative boundaries.

**Network Topology Overview:**
LAN, DMZ, and WAN interfaces are connected to respective ports of the FortiGate VM firewall. However, correct access permissions, interface roles, and aliases must be properly assigned to ensure secure operations.

**Interface Configuration Concepts:**
- **Alias:** Descriptive naming allows easy navigation and management
- **Interface Roles:** Proper role assignment optimizes FortiOS security features for each zone
- **Administrative Access:** Granular control over management access from different network zones

**IMPORTANT:** The management interface is the backbone of this lab environment. DO NOT EDIT THE MANAGEMENT INTERFACE as it's used for system access and grading purposes.

![interface-configuration-fortios-gui](assets/screenshots/interface-configuration-fortios-gui.png)

**Scenario:** Configuring FortiGate interfaces for enterprise network segmentation with appropriate security controls and administrative access restrictions.

**Task:** Implementing Enterprise Interface Configuration
```bash
# Access FortiGate VM
Address: https://fortigate.lab
Username: student
Password: student

# Navigate to Network -> Interfaces
# Right-click on interface and select Edit

# Configure Port 2 (WAN Interface)
Alias: WAN
Role: WAN
Administrative Access: PING

# Configure Port 3 (LAN Interface)  
Alias: LAN
Role: LAN
Administrative Access: HTTPS, HTTP, SSH, PING

# Configure Port 4 (DMZ Interface)
Alias: DMZ
Role: DMZ
Administrative Access: None
```

**Security Configuration Details:**

**Port 2 (WAN Configuration):**
- **Alias: WAN** - Clear identification for external-facing interface
- **Role: WAN** - Enables WAN-specific security features and optimizations
- **Administrative Access: PING** - Minimal access reduces external attack surface

**Port 3 (LAN Configuration):**
- **Alias: LAN** - Internal network identification
- **Role: LAN** - Activates LAN-specific features and security policies
- **Administrative Access: HTTPS, HTTP, SSH, PING** - Full management access for internal administration

**Port 4 (DMZ Configuration):**
- **Alias: DMZ** - Demilitarized zone identification
- **Role: DMZ** - DMZ-specific security controls and isolation features
- **Administrative Access: None** - No management access following security best practices

## Virtual IP Configuration

### Understanding External Service Exposure

Currently, the web server in the DMZ is not accessible from external networks, preventing outside users from accessing hosted web applications. Virtual IPs provide secure redirection of HTTP and HTTPS requests from the firewall's WAN interface to the DMZ web server, similar to iptables destination NAT functionality.

Virtual IPs can be created through the firewall web interface by navigating to **Policy & Objects -> Virtual IPs**. The FortiGate web GUI allows you to name Virtual IPs, choose IP address linking, specify protocols, and configure source/destination port ranges.

### HTTP Virtual IP Implementation

**Scenario:** Implementing secure external access to DMZ web server through FortiGate Virtual IP configuration for enterprise web application hosting.

![virtual-ip-http-creation-fortios](assets/screenshots/virtual-ip-http-creation-fortios.png)

**Task:** Creating HTTP Virtual IP Configuration
```bash
# Navigate to Policy & Objects -> Virtual IPs
# Click + Create New

# HTTP Virtual IP Configuration
Name: web server - HTTP
Interface: WAN (port2)
External IP Address: FortiGate's WAN IP address
Mapped IP Address: Web Server's IP address
Port Forwarding: Enabled
  - Protocol: TCP
  - External Service Port: 80
  - Map to Port: 80

# Click OK to save configuration
```

### HTTPS Virtual IP Implementation

![virtual-ip-https-creation-fortios](assets/screenshots/virtual-ip-https-creation-fortios.png)

**Task:** Creating HTTPS Virtual IP Configuration
```bash
# Create second Virtual IP for HTTPS
# Click + Create New

# HTTPS Virtual IP Configuration
Name: web server - HTTPS
Interface: WAN (port2)
External IP Address: FortiGate's WAN IP address
Mapped IP Address: Web Server's IP address
Port Forwarding: Enabled
  - Protocol: TCP
  - External Service Port: 443
  - Map to Port: 443

# Click OK to save configuration
```

**Virtual IP Security Features:**
- **Port-Specific Mapping:** Granular control over externally accessible services
- **Protocol Enforcement:** TCP specification ensures proper traffic handling
- **Address Translation:** Conceals internal network architecture from external threats
- **Service Isolation:** Individual Virtual IPs enable fine-grained access control and monitoring

## Firewall Policy Implementation

### Understanding Enterprise Policy Architecture

Firewall policies are the backbone of Fortinet firewall solutions. All traffic flowing through FortiGate VM must be associated with a policy. These policies control traffic destinations, processing methods, and allow/deny decisions for network communications.

By completing previous objectives, you've assigned correct roles and access permissions to ports and created links between the firewall's WAN interface and DMZ web server. However, the current firewall state includes a default "Implicit Deny" policy that blocks all traffic. You can observe this behavior on the **Policy & Objects -> Firewall Policy** page and verify through logs in **Log & Report -> Forward Traffic**.

**Policy Creation Overview:**
Firewall policies can be added/edited/removed via **Policy & Objects -> Firewall Policy**. The "+ Create New" button allows policy creation with the following required fields:

- **Name:** Descriptive policy identification for management
- **Incoming Interface:** Traffic entry point into FortiGate
- **Outgoing Interface:** Traffic exit point from FortiGate
- **Source:** Traffic origin addresses (specific IPs, networks, or "all")
- **Destination:** Traffic destination addresses (specific IPs, networks, or "all")
- **Service:** Protocols and ports this policy handles
- **Action:** Accept or deny traffic matching policy criteria

### WAN to DMZ Policy Configuration

**Scenario:** Enabling secure external access to DMZ services while maintaining strict access controls for enterprise web application hosting.

![firewall-policy-wan-to-dmz-fortios](assets/screenshots/firewall-policy-wan-to-dmz-fortios.png)

**Task:** Implementing External Access Policy
```bash
# Navigate to Policy & Objects -> Firewall Policy
# Click + Create New

# WAN to DMZ Policy Configuration
Name: WAN to DMZ
Incoming Interface: WAN (port2)
Outgoing Interface: DMZ (port4)
Source: all
Destination: web server - HTTP, web server - HTTPS
Schedule: always
Service: HTTP, HTTPS
Action: Accept
NAT: Disabled

# Click OK to save policy
```

**Important NAT Configuration Note:**
Do NOT enable NAT for this policy. Network address translation is handled by the Virtual IPs created in previous steps. If NAT were enabled, the web server would see the firewall's IP address as the source, losing valuable user information for logging and security analysis.

### LAN to DMZ Policy Configuration

**Scenario:** Providing internal users with secure access to DMZ services using internal IP addresses while maintaining network segmentation and security boundaries.

Currently, internal network users cannot access the web server using its internal IP address due to the lack of appropriate policies. This policy enables LAN users to access the DMZ web server directly via internal addressing, supporting future DMZ server additions.

![firewall-policy-lan-to-dmz-fortios](assets/screenshots/firewall-policy-lan-to-dmz-fortios.png)

**Task:** Implementing Internal DMZ Access Policy
```bash
# Create new Firewall Policy
# Click + Create New

# LAN to DMZ Policy Configuration
Name: LAN to DMZ
Incoming Interface: LAN (port3)
Outgoing Interface: DMZ (port4)
Source: all
Destination: all
Schedule: always
Service: HTTP, HTTPS, PING, SSH
Action: Accept
NAT: Disabled

# Click OK to save policy
```

**NAT Consideration:**
Do NOT enable NAT for this policy. Maintaining original source IP addresses is crucial for security logging, user tracking, and forensic investigations when internal users access DMZ services.

### LAN to WAN Policy Configuration

**Scenario:** Enabling secure internet access for internal users while implementing NAT for proper external communication and maintaining security through traffic monitoring.

Currently, LAN users cannot access the internet despite the firewall being configured as the default gateway. All traffic routes to the firewall but gets dropped due to the implicit deny policy. This behavior can be observed in **Log & Report -> Forward Traffic** section.

![firewall-policy-lan-to-wan-fortios](assets/screenshots/firewall-policy-lan-to-wan-fortios.png)

**Task:** Implementing Internet Access Policy
```bash
# Create new Firewall Policy
# Click + Create New

# LAN to WAN Policy Configuration
Name: LAN to WAN
Incoming Interface: LAN (port3)
Outgoing Interface: WAN (port2)
Source: all
Destination: all
Schedule: always
Service: ALL
Action: Accept
NAT: Enabled

# Click OK to save policy
```

**Critical NAT Requirement:**
NAT MUST be enabled for this policy. When NAT is enabled, source IP addresses are replaced with the firewall's WAN-facing IP address, enabling proper external communication and return traffic routing. This is essential for internet access functionality.

**Policy Security Summary:**
- **Traffic Direction Control:** Explicit interface specification ensures proper traffic flow management
- **Service Granularity:** Specific service selection minimizes unnecessary exposure and attack surface
- **Strategic NAT Deployment:** Balanced NAT configuration maintains functionality while preserving security logging capabilities
- **Source IP Preservation:** Maintaining source information supports comprehensive security monitoring and incident response

## Practical Exercises

### Exercise 1: Enterprise Network Segmentation Implementation

**Objective:** Design and implement comprehensive network segmentation for a growing enterprise requiring multiple security zones and strict access controls.

**Scenario:** Security team must implement proper network segmentation for a company expanding their digital infrastructure with web services, internal applications, guest networks, and secure management zones.

**Tasks:**

1. **Advanced Interface Configuration:**
```bash
# Configure additional security zones for enterprise environment
Configure Port 5 (GUEST Network):
- Alias: GUEST
- Role: LAN  
- Administrative Access: None
- VLAN ID: 100

Configure Port 6 (MANAGEMENT):
- Alias: MGMT
- Role: LAN
- Administrative Access: HTTPS, SSH (restricted source IPs)
- VLAN ID: 200

Configure Port 7 (SERVERS):
- Alias: SERVERS
- Role: DMZ
- Administrative Access: SSH (management subnet only)
- VLAN ID: 300
```

2. **Complex Virtual IP Configuration:**
```bash
# Create service-specific Virtual IPs for enterprise applications
Mail Server External Access:
- Name: exchange-server-external
- External Ports: 25, 587, 993, 995
- Internal Server: 10.0.3.50
- Load Balancing: Round-robin

API Gateway External Access:
- Name: api-gateway-external  
- External Port: 8443
- Internal Server: 10.0.3.100
- SSL Inspection: Enabled

Database External Management:
- Name: db-management-external
- External Port: 3306
- Internal Server: 10.0.3.200
- Source Restriction: Management IPs only
```

3. **Zero-Trust Policy Framework:**
```bash
# Implement restrictive inter-zone communication policies
GUEST Network Policies:
- GUEST to LAN: Deny all
- GUEST to DMZ: HTTP/HTTPS to web servers only
- GUEST to SERVERS: Deny all
- GUEST to WAN: HTTP/HTTPS only with content filtering

SERVER Zone Policies:
- SERVERS to LAN: Database ports only (3306, 1433, 5432)
- SERVERS to WAN: HTTPS, NTP, DNS only
- SERVERS to DMZ: Application-specific ports only
- LAN to SERVERS: Management protocols (SSH, SNMP, RDP)

Management Network Policies:
- MGMT to all zones: Administrative access only
- All zones to MGMT: Deny (except logging and monitoring)
- MGMT to WAN: Security updates and threat feeds only
```

### Exercise 2: Incident Response Firewall Configuration

**Objective:** Configure dynamic firewall policies supporting incident response procedures, forensic investigations, and business continuity during security events.

**Scenario:** SOC team detects advanced persistent threat (APT) activity requiring immediate containment measures while maintaining investigative capabilities and critical business operations.

**Tasks:**

1. **Emergency Containment Policies:**
```bash
# Create rapid isolation capabilities for compromised systems
Quarantine Zone Configuration:
- Interface: Port 8 (QUARANTINE)
- Alias: QUARANTINE
- Role: DMZ
- Administrative Access: SSH (SOC team source IPs only)

Containment Policies:
- QUARANTINE to LAN: Deny all
- QUARANTINE to WAN: Deny all  
- QUARANTINE to DMZ: Deny all
- LAN to QUARANTINE: SOC forensic tools only (22, 3389, 5985)
- WAN to QUARANTINE: Deny all

Dynamic Policy Creation:
- Source-based quarantine policies
- Time-limited containment rules
- Emergency bypass procedures for critical systems
```

2. **Forensic Investigation Access:**
```bash
# Enable secure forensic access while maintaining containment
SOC Analyst Access Policies:
- Source: SOC analyst workstations (10.0.100.0/24)
- Destination: Quarantine zone
- Services: SSH, RDP, WinRM, Custom forensic ports (9999, 8888)
- Schedule: 24/7 during incident
- Logging: Enhanced logging with packet capture
- Authentication: Certificate-based with MFA

Forensic Tool Communication:
- Network forensic scanner access
- Memory dump collection (custom ports)
- Log aggregation and SIEM integration
- Secure evidence transfer protocols
```

3. **Business Continuity Framework:**
```bash
# Maintain critical operations during incident response
Critical Business Policies:
- Essential services bypass: Pre-approved critical systems list
- Emergency communication channels: Secure messaging (443, 8443)
- Backup system activation: Automated failover policies
- Vendor remote support: Temporary VPN access with restrictions

Incident Communication Policies:
- Management notification systems
- Customer communication channels
- Regulatory reporting requirements
- Media and public relations coordination
```

### Exercise 3: Advanced Compliance and Security Monitoring

**Objective:** Implement comprehensive security controls and monitoring capabilities meeting enterprise compliance requirements and advanced threat detection.

**Scenario:** Enterprise must implement advanced security controls for PCI DSS, GDPR, and SOX compliance while maintaining operational efficiency and comprehensive security monitoring.

**Tasks:**

1. **Regulatory Compliance Implementation:**
```bash
# PCI DSS Compliance Configuration
Cardholder Data Environment (CDE) Isolation:
- Dedicated CDE interface and VLAN
- Strict segmentation from corporate networks
- Multi-factor authentication requirements
- Encrypted communication enforcement (TLS 1.3 minimum)

CDE Access Policies:
- Source: Authorized personnel only (certificate-based)
- Destination: CDE systems (10.0.50.0/24)
- Services: HTTPS, SSH with key-based auth only
- Schedule: Business hours with exception procedures
- Monitoring: Real-time transaction monitoring

# GDPR Compliance Configuration
Data Processing System Isolation:
- Personal data processing zone segregation
- Data minimization through traffic filtering
- Consent-based access controls
- Data retention policy enforcement through automated rules
```

2. **Advanced Threat Detection Integration:**
```bash
# Enhanced Security Monitoring Configuration
FortiGuard Integration:
- Antivirus scanning on all policies
- Web filtering with category-based blocking
- Application control with custom signatures
- Intrusion Prevention System (IPS) deployment

Advanced Threat Protection:
- Sandbox integration for unknown files
- DNS filtering for malicious domains
- SSL inspection for encrypted threat detection
- Behavioral analysis for anomaly detection

SIEM Integration:
- Real-time log streaming to central SIEM
- Custom log formats for security analysis
- Threat intelligence feed integration
- Automated alert generation and response
```

3. **High Availability and Performance Optimization:**
```bash
# Enterprise High Availability Configuration
Active-Passive Clustering:
- Primary and secondary FortiGate configuration
- Configuration synchronization procedures
- Automatic failover testing and validation
- Session state synchronization

Load Balancing Implementation:
- Traffic distribution across multiple paths
- Health monitoring for backend services
- Geographic load balancing for global access
- Application-aware load balancing policies

Performance Optimization:
- Traffic shaping and QoS implementation
- Connection rate limiting for DDoS protection
- Caching and acceleration features
- Bandwidth management for critical applications
```

## Lab Validation

### Knowledge Check Questions

**Q1:** What is the primary security benefit of assigning specific roles to FortiGate interfaces?
**A:** Interface roles optimize FortiOS security features and hide unrelated settings, reducing configuration complexity and improving security posture.

**Q2:** Why should NAT be disabled for WAN to DMZ policies when using Virtual IPs?
**A:** Virtual IPs handle address translation, and additional NAT would cause the web server to see the firewall's IP as source, losing valuable user information for security logging and analysis.

**Q3:** What administrative access should be configured for DMZ interfaces in enterprise environments?
**A:** None - DMZ interfaces should have no administrative access to reduce attack surface and prevent unauthorized firewall management from potentially compromised DMZ systems.

**Q4:** When is NAT required in FortiGate firewall policies?
**A:** NAT is required for LAN to WAN policies to enable internet access by translating internal private IP addresses to the firewall's public WAN IP address.

**Q5:** What is the purpose of the "Implicit Deny" policy in FortiGate firewalls?
**A:** The implicit deny policy blocks all traffic that doesn't match explicit allow policies, providing a secure-by-default configuration that requires explicit permission for all communications.

**Q6:** How do Virtual IPs differ from traditional port forwarding in enterprise security?
**A:** Virtual IPs provide more granular control, support advanced traffic inspection, enable integration with security services, and offer better logging and monitoring capabilities for enterprise environments.

### Practical Validation

Demonstrate proficiency by completing these tasks:

- **Interface Configuration Mastery:** Successfully configure all interface types with appropriate roles and administrative access restrictions
- **Virtual IP Implementation:** Create and test both HTTP and HTTPS Virtual IPs with proper port forwarding configuration
- **Policy Framework Deployment:** Implement all three core policy types (WAN→DMZ, LAN→DMZ, LAN→WAN) with correct NAT settings
- **Security Validation:** Verify policy effectiveness through traffic testing, log analysis, and security boundary validation

### Advanced Challenge

**Scenario:** Multi-Location Enterprise Security Architecture

Design and implement a comprehensive firewall architecture for a multinational corporation with multiple office locations, cloud integration, and strict regulatory requirements:

1. **Global Network Architecture Design:**
   - Implement 8-zone network architecture (WAN, LAN, DMZ, GUEST, SERVERS, MGMT, CLOUD, PARTNER)
   - Configure site-to-site VPN policies for inter-office communication
   - Implement geo-location based access controls and traffic routing
   - Design disaster recovery and business continuity firewall policies

2. **Advanced Service Exposure Strategy:**
   - Configure multiple Virtual IP pools for load balancing and high availability
   - Implement SSL offloading and acceleration for performance optimization
   - Create geo-distributed service exposure with intelligent traffic routing
   - Deploy advanced threat protection integration across all external services

3. **Comprehensive Compliance Framework:**
   - Implement multi-regulatory compliance (PCI DSS, GDPR, HIPAA, SOX)
   - Create detailed audit logging and reporting capabilities
   - Deploy data loss prevention (DLP) integration through firewall policies
   - Establish comprehensive incident response and forensic capabilities

**Success Criteria:**
- Demonstrate comprehensive understanding of enterprise FortiGate architecture
- Implement security controls meeting multiple compliance requirements simultaneously
- Create scalable and maintainable firewall policy frameworks
- Establish effective monitoring, logging, and incident response capabilities

## Key Takeaways

### Technical Skills Developed

- **FortiOS Administration:** Comprehensive understanding of enterprise FortiGate configuration, interface management, and policy implementation
- **Virtual IP Mastery:** Advanced techniques for secure external service exposure with proper address translation and traffic handling
- **Policy Architecture:** Systematic approach to firewall policy design supporting network segmentation and security enforcement
- **NAT Implementation:** Strategic use of Network Address Translation for secure communications and proper traffic routing
- **Security Integration:** Understanding of FortiGate integration with enterprise security tools and monitoring systems

### Security Applications

- **Perimeter Defense:** Advanced firewall configuration providing robust protection against external threats and unauthorized access
- **Network Segmentation:** Comprehensive implementation of network zones supporting defense-in-depth security architectures
- **Incident Response:** Firewall configuration supporting rapid containment, forensic investigation, and business continuity during security events
- **Compliance:** Implementation of firewall policies supporting regulatory requirements and organizational security policies
- **Threat Prevention:** Integration with FortiGuard services and advanced threat protection for real-time security enforcement

### Professional Development

- **SOC Operations:** Advanced firewall management skills essential for security operations center environments and incident response teams
- **Security Engineering:** Expertise in designing and implementing enterprise network security architectures using industry-leading platforms
- **Enterprise Networking:** Comprehensive understanding of network security principles applicable to large-scale corporate environments
- **Compliance Management:** Skills for implementing and maintaining firewall configurations supporting regulatory and policy requirements
- **Incident Response:** Capabilities for rapid firewall reconfiguration and security policy adjustment during active security incidents

## 🔧 Next Steps

### Advanced Preparation

- Explore FortiGate High Availability clustering and advanced redundancy configuration for enterprise environments
- Study FortiAnalyzer integration for centralized logging, reporting, and security event correlation across multiple devices
- Learn FortiManager capabilities for centralized device management and policy deployment in large-scale environments
- Research advanced FortiGuard services integration including sandboxing, threat intelligence, and behavioral analysis

## Additional Resources

- [FortiGate Administration Guide](https://docs.fortinet.com/product/fortigate) - Comprehensive FortiOS configuration and management documentation
- [FortiOS Virtual IP Configuration](https://docs.fortinet.com/document/fortigate/7.0.0/administration-guide/522816) - Detailed Virtual IP implementation guide
- [Enterprise Firewall Security Best Practices](https://www.fortinet.com/resources/cyberglossary/firewall-best-practices) - Industry best practices for enterprise firewall deployment
- [Network Segmentation Security Guide](https://www.sans.org/white-papers/network-segmentation/) - SANS guidance on network segmentation strategies
- [CompTIA Security+ Study Guide](https://www.comptia.org/certifications/security) - Foundation certification supporting firewall security concepts

**Lab Completed:** Firewall Basic Policies - FortiOS  
**Focus Area:** Enterprise Network Security & Perimeter Defense  
**Series Progress:** 1 of 7 labs completed

---

*Building enterprise firewall foundations for cybersecurity excellence*
