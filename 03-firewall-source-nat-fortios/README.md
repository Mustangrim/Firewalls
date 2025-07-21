# FortiOS Source NAT Configuration Lab

## Lab Overview

**Lab Focus:** Advanced Source NAT implementation and enterprise network architecture design  
**Difficulty:** Advanced    
**Author:** Mykyta Palamarchuk  
**Role:** SOC Analyst | Incident Response Specialist  
**Certification:** CompTIA Security+ (SY0-701)

### Environment Specifications
- **Platform:** FortiGate VM (FortiOS)
- **Scenario:** Corporate network expansion with multi-tier application architecture
- **Business Context:** New office location with vacation planner system deployment
- **Network Architecture:** Three-zone design (WAN/LAN/DMZ) with granular NAT policies

---

## Learning Objectives

Upon completion of this lab, participants will be able to:

1. **Design enterprise NAT architecture** supporting different traffic classes and security requirements
2. **Implement granular Source NAT policies** for servers, workstations, and network segments
3. **Configure IP pools and address objects** for scalable network address management
4. **Apply security best practices** for NAT policy design and network segmentation
5. **Troubleshoot NAT connectivity issues** using systematic analysis and validation techniques
6. **Optimize network performance** through appropriate NAT type selection and policy ordering

---

## Business Scenario

### Corporate Context
You have been hired as a junior network engineer at **Commensurate Technology**. The corporation is establishing a new office location protected by a Fortinet firewall. This project is critical for maintaining consistent perimeter security standards across all corporate sites.

### Application Architecture
The new location hosts a **vacation planner system** with a modern frontend-backend architecture:

- **Frontend Server (DMZ):** Public-facing web application receiving internet connections
- **Backend Server (LAN):** Internal application server handling business logic and data processing
- **User Workstations (LAN):** Employee devices requiring internet access for productivity

### Security Requirements
- **Granular visibility:** Separate NAT policies for different system classes enable targeted monitoring
- **Enhanced security:** Dedicated public IPs for critical servers improve security posture
- **Compliance support:** Detailed traffic logging and filtering capabilities for audit requirements

---

## NAT Technology Foundation

### Network Address Translation Overview

NAT is a critical firewall function enabling private IPv4 networks to communicate with the internet while conserving public IP addresses and providing network security through address obfuscation.

**Source NAT Process:**
1. **Outbound Translation:** Private LAN addresses → Public routable addresses
2. **Session Tracking:** Firewall maintains translation state table in memory
3. **Return Traffic Handling:** Incoming packets matched to original sessions and translated back

### NAT Implementation Types

#### 1. One-to-One NAT
**Use Case:** Dedicated servers requiring consistent public IP addresses

**Characteristics:**
- Single private IP ↔ Single public IP mapping
- Preserves port numbers and protocols
- Ideal for servers requiring inbound connections
- Provides consistent external identity for services

![One-to-One NAT Architecture](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/nat-one-to-one-diagram.png)

**Example Implementation:**
```
Private Server: 192.168.1.100 → Public IP: 203.0.113.10
All protocols and ports preserved in translation
```

#### 2. Many-to-Many NAT
**Use Case:** Multiple servers or network segments requiring dedicated public addresses

**Characteristics:**
- Equal numbers of private and public IPs required
- Public IP pools managed by ISP allocation
- Maintains one-to-one relationships within pools
- Suitable for server farms and multi-tenant environments

![Many-to-Many NAT Architecture](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/nat-many-to-many-diagram.png)

**Pool Configuration Example:**
```
Private Pool: 192.168.1.100-110 (10 addresses)
Public Pool: 203.0.113.10-19 (10 addresses)
Dynamic assignment within pool ranges
```

#### 3. Overload NAT (PAT - Port Address Translation)
**Use Case:** Large numbers of internal clients sharing limited public IP addresses

**Characteristics:**
- Many private IPs → Single public IP
- Port number manipulation for session uniqueness
- Most common NAT implementation for user networks
- Maximizes public IP address utilization

![Overload NAT Architecture](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/nat-overload-diagram.png)

**Translation Example:**
```
Client A (192.168.1.50:1024) → Public IP (203.0.113.1:35000)
Client B (192.168.1.51:1024) → Public IP (203.0.113.1:35001)
Client C (192.168.1.52:1024) → Public IP (203.0.113.1:35002)
```

---

## Knowledge Validation

### NAT Concepts Assessment

**Question 1:** Which statements are true about NAT implementation?

A) NAT is used because of the shortage of public IP addresses  
B) NAT is never configured on firewalls  
C) Overload NAT enables many LAN hosts to access the Internet  
D) One-to-one NAT is typically used to map private server addresses to public ones  
E) Separating source NAT rules for servers and LAN hosts is not relevant

**✅ Correct Answers:** A, C, D

**Rationale:**
- **A:** IPv4 address exhaustion drives NAT adoption for private networks
- **C:** Overload NAT (PAT) enables efficient public IP utilization for multiple clients
- **D:** One-to-one NAT provides consistent public identity for servers
- **B:** Incorrect - Firewalls commonly implement NAT functionality
- **E:** Incorrect - Separate NAT rules enable granular security controls and monitoring

**Question 2:** What does NAT stand for?

**✅ Answer:** Network Address Translation

---

## Lab Architecture

### Network Topology
```
Internet (WAN)
     ↓
[FortiGate Firewall]
   ↙    ↓    ↘
DMZ    LAN    Management
(Frontend) (Backend + Users)

Segmentation Strategy:
- WAN Interface (port1): Internet connectivity
- DMZ Interface (port4): Frontend web server
- LAN Interface (port3): Backend server + user workstations
```

### IP Addressing Scheme
- **WAN Network:** 10.0.0.0/24 (Public IP simulation)
- **DMZ Network:** 10.0.1.0/24 (Frontend services)
- **LAN Network:** 10.0.3.0/24 (Backend services + users)

### NAT Design Goals
1. **DMZ Server:** Dedicated public IP (10.0.0.4) for frontend services
2. **LAN Server:** Separate public IP (10.0.0.5) for backend communications
3. **LAN Users:** Shared WAN interface IP (10.0.0.3) for general internet access

---

## Lab Exercises

### Exercise 1: Network Object Creation and Management

**Scenario:** Create granular network objects representing different system classes to enable precise NAT policy implementation.

#### Objective
Establish address objects for the three distinct traffic classes requiring different NAT treatments.

#### Access Information
- **URL:** `https://fortigate.lab`
- **Username:** `admin`
- **Password:** `student`

#### Task Implementation

**Create Frontend Server Object:**
1. Navigate to **Policy & Objects → Addresses**
2. Click **Create New**
3. Configure frontend server object:
   - **Name:** `FRONTEND-WEB-SERVER-DMZ-IP`
   - **Type:** `Subnet`
   - **Subnet/IP Range:** `10.0.1.2/32`
   - **Interface:** `DMZ (port4)`

![DMZ Server Object Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/dmz-server-object.png)

**Create Backend Server Object:**
1. Click **Create New** for second object
2. Configure backend server object:
   - **Name:** `BACKEND-WEB-SERVER-LAN-IP`
   - **Type:** `Subnet`
   - **Subnet/IP Range:** `10.0.3.2/32`
   - **Interface:** `LAN (port3)`

![LAN Server Object Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/lan-server-object.png)

**Create User Network Object:**
1. Click **Create New** for network object
2. Configure user network object:
   - **Name:** `LAN-machines`
   - **Type:** `Subnet`
   - **Subnet/IP Range:** `10.0.3.0/24`
   - **Interface:** `LAN (port3)`

![LAN Machines Object Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/lan-machines-object.png)

#### Validation
- Verify all three objects appear in the address list
- Confirm proper IP addressing and interface assignments
- Test object usability in policy creation dropdown menus

---

### Exercise 2: IP Pool Configuration for Dedicated NAT

**Scenario:** Configure IP pools defining public address allocations for one-to-one NAT implementations supporting critical server infrastructure.

#### Objective
Create dedicated IP pools enabling consistent public IP assignments for frontend and backend servers.

#### Technical Background
**IP Pools vs Address Objects:**
- **Address Objects:** Define private network resources
- **IP Pools:** Define public IP allocations for NAT translation
- **Relationship:** NAT policies map address objects to IP pools

#### Implementation Tasks

**Create Frontend Server IP Pool:**
1. Navigate to **Policy & Objects → IP Pools**
2. Click **Create New**
3. Configure frontend pool:
   - **Name:** `FRONTEND-WEB-SERVER-WAN-IP`
   - **Type:** `One-to-One`
   - **External IP Range:**
     - **Start IP:** `10.0.0.4`
     - **End IP:** `10.0.0.4`
   - **Interface:** `WAN (port1)`

![Frontend IP Pool Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/frontend-ip-pool.png)

**Create Backend Server IP Pool:**
1. Click **Create New** for second pool
2. Configure backend pool:
   - **Name:** `BACKEND-WEB-SERVER-WAN-IP`
   - **Type:** `One-to-One`
   - **External IP Range:**
     - **Start IP:** `10.0.0.5`
     - **End IP:** `10.0.0.5`
   - **Interface:** `WAN (port1)`

![Backend IP Pool Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/backend-ip-pool.png)

#### Security Considerations
- **Dedicated IPs:** Enable precise traffic monitoring and logging
- **One-to-One Mapping:** Maintains session consistency for server applications
- **Pool Management:** Facilitates scalable IP address allocation and tracking

---

### Exercise 3: DMZ Server Source NAT Policy

**Scenario:** Configure dedicated Source NAT for the frontend web server enabling internet connectivity with a consistent public IP address for external service integration.

#### Objective
Implement one-to-one Source NAT providing the DMZ web server with dedicated internet connectivity using a specific public IP address.

#### Business Justification
- **Service Integration:** External APIs and services require consistent source IP
- **Security Monitoring:** Dedicated IP enables targeted threat detection and response
- **Compliance Logging:** Separate traffic streams support audit and regulatory requirements

#### Policy Configuration

**Create DMZ Source NAT Policy:**
1. Navigate to **Policy & Objects → Firewall Policy**
2. Click **Create New**
3. Configure policy parameters:
   - **Name:** `WEB-SERVER-DMZ-source-NAT`
   - **Incoming Interface:** `DMZ (port4)`
   - **Outgoing Interface:** `WAN (port1)`
   - **Source:** `FRONTEND-WEB-SERVER-DMZ-IP`
   - **Destination:** `all`
   - **Schedule:** `always`
   - **Service:** `ALL`
   - **Action:** `ACCEPT`

**Configure NAT Settings:**
1. Scroll to NAT section
2. **Enable NAT:** ✅ Checked
3. **IP Pool Configuration:** Select `FRONTEND-WEB-SERVER-WAN-IP`

#### Technical Implementation Details
- **Traffic Flow:** DMZ Server (10.0.1.2) → Internet via Public IP (10.0.0.4)
- **Session Tracking:** FortiGate maintains NAT translation table for return traffic
- **Port Preservation:** All ports and protocols preserved in translation

---

### Exercise 4: LAN Server Source NAT Policy

**Scenario:** Configure separate Source NAT for the backend web server enabling secure internet connectivity with dedicated public IP for backend service communications.

#### Objective
Implement one-to-one Source NAT for the LAN web server using a distinct public IP address from the frontend server.

#### Architecture Benefits
- **Service Separation:** Backend traffic isolated from frontend traffic streams
- **Security Analysis:** Separate public IPs enable granular security monitoring
- **Performance Optimization:** Dedicated NAT sessions reduce translation conflicts

#### Policy Implementation

**Create LAN Source NAT Policy:**
1. Access **Policy & Objects → Firewall Policy**
2. Click **Create New**
3. Configure policy settings:
   - **Name:** `WEB-SERVER-LAN-source-NAT`
   - **Incoming Interface:** `LAN (port3)`
   - **Outgoing Interface:** `WAN (port1)`
   - **Source:** `BACKEND-WEB-SERVER-LAN-IP`
   - **Destination:** `all`
   - **Schedule:** `always`
   - **Service:** `ALL`
   - **Action:** `ACCEPT`

**NAT Configuration:**
1. Navigate to NAT section
2. **Enable NAT:** ✅ Checked
3. **IP Pool Selection:** `BACKEND-WEB-SERVER-WAN-IP`

![LAN Server NAT Policy](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/lan-server-nat-policy.png)

#### Validation Steps
- **Connectivity Test:** Verify backend server can reach internet services
- **IP Verification:** Confirm external services see source IP as 10.0.0.5
- **Log Analysis:** Validate NAT translations appear correctly in traffic logs

---

### Exercise 5: LAN User Network Overload NAT

**Scenario:** Configure overload NAT (PAT) for general user network enabling efficient internet access for multiple concurrent users through shared WAN interface IP.

#### Objective
Implement many-to-one NAT allowing all LAN users to share the firewall's primary WAN IP address for internet connectivity.

#### Technical Approach
**Overload NAT Benefits:**
- **IP Efficiency:** Multiple users share single public IP
- **Port Management:** Automatic port allocation prevents conflicts  
- **Scalability:** Supports large numbers of concurrent user sessions
- **Cost Optimization:** Minimizes public IP address requirements

#### Policy Configuration

**Create LAN Users NAT Policy:**
1. Navigate to **Policy & Objects → Firewall Policy**
2. Click **Create New**
3. Configure comprehensive user access:
   - **Name:** `NAT-LAN-machines`
   - **Incoming Interface:** `LAN (port3)`
   - **Outgoing Interface:** `WAN (port1)`
   - **Source:** `LAN-machines`
   - **Destination:** `all`
   - **Schedule:** `always`
   - **Service:** `ALL`
   - **Action:** `ACCEPT`

**Overload NAT Settings:**
1. Access NAT configuration section
2. **Enable NAT:** ✅ Checked
3. **IP Pool Configuration:** Select `Use Outgoing Interface Address`

#### Implementation Result
- **Traffic Translation:** All LAN users (10.0.3.0/24) → WAN IP (10.0.0.3)
- **Port Differentiation:** Unique source ports distinguish user sessions
- **Session Management:** FortiGate tracks thousands of concurrent connections

---

## Advanced Configuration Considerations

### NAT Policy Ordering and Optimization

**Rule Precedence Impact:**
```
Optimal Policy Order:
1. Specific server NAT policies (one-to-one)
2. Network segment NAT policies (many-to-many)
3. General user NAT policies (overload)
4. Deny-all policies (security catch-all)
```

**Performance Optimization:**
- Position frequently-matched policies higher in the list
- Combine similar policies where security requirements allow
- Use address groups for multiple similar objects
- Monitor policy hit counts for optimization opportunities

### Security Architecture Integration

**Logging and Monitoring Strategy:**
- **Separate NAT policies** enable granular log analysis
- **Dedicated IP addresses** support targeted threat detection
- **Policy-specific logging** improves incident response capabilities
- **Session correlation** links internal and external traffic flows

**Network Segmentation Benefits:**
- **Traffic isolation** between server tiers and user networks
- **Security zone enforcement** through interface-specific policies  
- **Attack surface reduction** via principle of least privilege
- **Compliance alignment** with regulatory segmentation requirements

### Troubleshooting NAT Connectivity

**Common Issues and Solutions:**

1. **Asymmetric Routing Problems**
   - **Symptom:** Outbound connections fail or timeout
   - **Solution:** Verify return traffic paths and routing tables

2. **NAT Pool Exhaustion**
   - **Symptom:** New connections rejected
   - **Solution:** Monitor pool utilization and expand ranges

3. **Policy Ordering Conflicts**
   - **Symptom:** Wrong NAT policy applied to traffic
   - **Solution:** Review rule precedence and reorder policies

4. **Session Table Overflow**
   - **Symptom:** Connection establishment failures
   - **Solution:** Optimize session timeouts and increase table size

---

## Validation and Testing

### Functional Testing Methodology

**Connectivity Validation:**
1. **DMZ Server Testing:**
   - Verify internet access using dedicated public IP (10.0.0.4)
   - Confirm external services receive correct source IP
   - Test bidirectional connectivity for server applications

2. **LAN Server Testing:**
   - Validate backend server internet access via IP (10.0.0.5)
   - Verify separation from DMZ server traffic streams
   - Test API and external service integrations

3. **User Network Testing:**
   - Confirm general user internet access functionality
   - Verify port allocation and session management
   - Test concurrent user capacity and performance

### Security Monitoring and Analysis

**Traffic Flow Verification:**
- **Policy Hit Analysis:** Monitor rule utilization and effectiveness
- **NAT Session Tracking:** Analyze translation table efficiency
- **Log Correlation:** Link internal activities to external connections
- **Anomaly Detection:** Identify unusual traffic patterns or volumes

**Performance Metrics:**
- **Translation Latency:** Monitor NAT processing delays
- **Session Capacity:** Track concurrent connection limits
- **Throughput Analysis:** Measure bandwidth utilization per policy
- **Resource Consumption:** Monitor CPU and memory usage for NAT operations

---

## Lab Summary

This advanced Source NAT lab demonstrates enterprise-grade network architecture design implementing granular traffic management and security controls. The multi-tier approach provides enhanced visibility, security, and performance optimization for modern business networks.

**Key Technical Achievements:**
- **Granular NAT Architecture:** Separate policies for different traffic classes
- **Optimized IP Utilization:** Combination of one-to-one and overload NAT techniques  
- **Enhanced Security Posture:** Dedicated public IPs for critical infrastructure
- **Scalable Design:** Framework supporting future network expansion
- **Operational Efficiency:** Simplified management through logical object grouping

**Real-World Applications:**
- **Enterprise Network Design:** Multi-site corporate network implementations
- **Cloud Migration Planning:** Hybrid network architecture development
- **Security Architecture:** Network segmentation and access control design
- **Compliance Frameworks:** Audit trail and monitoring capability development
- **Performance Optimization:** Traffic engineering and capacity planning

**Career Development Value:**
- **Advanced Networking Skills:** Deep understanding of NAT implementations and optimization
- **Security Architecture Knowledge:** Enterprise-grade network security design principles
- **Operational Excellence:** Best practices for network management and troubleshooting
- **Business Alignment:** Technology solutions supporting organizational objectives

---

*This advanced lab builds essential skills for network security professionals working with enterprise firewall implementations. The concepts and configurations demonstrated here directly apply to production environments supporting business-critical applications and services.*
