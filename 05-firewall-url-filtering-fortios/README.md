# FortiOS URL Filtering Security Lab

## Lab Overview

**Lab Focus:** Advanced web content filtering and URL-based threat prevention using FortiGate NGFW  
**Difficulty:** Advanced    
**Author:** Mykyta Palamarchuk  
**Role:** SOC Analyst | Incident Response Specialist  
**Certification:** CompTIA Security+ (SY0-701)

### Environment Specifications
- **Platform:** FortiGate VM (FortiOS NGFW)
- **Security Framework:** ISMS (Information Security Management System) compliance
- **Technology Focus:** Web filtering, URL categorization, and content control
- **Business Context:** Corporate acceptable use policy enforcement and productivity optimization

---

## Learning Objectives

Upon completion of this lab, participants will be able to:

1. **Implement comprehensive web filtering policies** using FortiGate URL filtering capabilities
2. **Configure multiple filtering methods** including simple, wildcard, and regex patterns
3. **Design department-specific web filtering profiles** based on business and security requirements
4. **Integrate web filtering with firewall policies** for comprehensive network security
5. **Troubleshoot web access issues** using FortiGate logging and analysis tools
6. **Apply security best practices** for web content filtering and threat prevention
7. **Validate filtering effectiveness** through systematic testing and log analysis

---

## Business Scenario

### Corporate Security Requirements
Your organization's **Chief Information Security Officer (CISO)** has mandated the implementation of comprehensive web filtering controls to achieve **ISMS (Information Security Management System)** compliance and address productivity concerns.

**Current Challenges:**
- **Information Leakage Risk:** Employees accessing cloud storage and file sharing services
- **Productivity Issues:** Accounting department accessing news sites and social media during work hours
- **Security Compliance:** Need for granular web access controls and comprehensive logging
- **Policy Enforcement:** Requirement for different filtering policies per department

### Business Impact
**Accounting Manager Complaint:** "Internet access issues are affecting team productivity, and we need specific sites blocked that are reducing team performance."

**Security Requirements:**
- Block high-risk categories (cloud storage, social media, news sites)
- Maintain business functionality (DNS resolution, legitimate business sites)
- Implement granular controls with comprehensive logging
- Support ISMS compliance and audit requirements

---

## FortiGate Web Filtering Architecture

### Web Filtering Components

FortiOS provides three main web filtering components that work together to provide comprehensive web security:

![Web Filtering Menu Structure](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/web-filter-menu-structure.png)

**1. Web Content Filter:**
- Blocks traffic based on content patterns or specific keywords
- Supports regular expressions for advanced pattern matching
- Ideal for blocking content regardless of source URL

**2. URL Filter:**
- Uses specific URLs and URL patterns to block content
- Integrates with FortiSandbox for malicious URL detection
- Supports multiple pattern types (simple, wildcard, regex)

**3. FortiGuard Web Filtering:**
- Provides category-based filtering using Fortinet's threat intelligence
- Real-time URL categorization and reputation scoring
- Comprehensive database with regular updates

![Web Filtering Components](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/web-filter-components.png)

### Processing Order
FortiGate web filtering features are applied in the following sequence:
1. **URL Filter** - Specific URL/pattern matching
2. **FortiGuard Web Filtering** - Category-based filtering  
3. **Web Content Filter** - Content pattern matching
4. **Web Script Filter** - Active content filtering
5. **Antivirus Scanning** - Malware detection and prevention

---

## Lab Environment Access

### System Credentials
- **URL:** `https://fortigate.lab`
- **Username:** `student`
- **Password:** `student`

### Network Architecture
```
Internet
    ↓
[FortiGate NGFW]
    ↓
Corporate Network (10.0.3.0/24)
    ↓
Accounting Department
(desktop-accounting-1: 10.0.3.2)
```

---

## Lab Exercises

### Exercise 1: Web Filtering Profiles Analysis

**Scenario:** Before implementing custom web filtering policies, security professionals must understand the default configurations and available options in FortiGate.

#### Objective
Analyze default web filtering profiles and understand their configurations to determine baseline security settings.

#### Default Profile Investigation

**Navigate to Web Filter Profiles:**
1. Access **Security Profiles → Web Filter**
2. Examine pre-configured profiles and their settings

#### Knowledge Assessment

**Question 1:** What are the 3 preloaded web filter profiles that come with FortiGate by default?

**Available Options:**
- default
- IT  
- monitor-all
- wired-default
- wireless-default
- wifi-default

**✅ Correct Answer:**
- `default`
- `wired-default` 
- `wireless-default`

**Question 2:** What is the action for the Alcohol category under the default web filter profile?

**✅ Answer:** `Block`

**Question 3:** What is the second Potentially Liable category allowed under the default web filter profile?

**✅ Answer:** `Dating`

#### Profile Analysis Insights
- **Default Profile:** Provides balanced security with minimal business disruption
- **Wired-Default:** Optimized for traditional wired network environments
- **Wireless-Default:** Configured with stricter controls for wireless networks
- **Category Actions:** Range from Allow to Block with monitoring and warning options

---

### Exercise 2: Traffic Analysis and Troubleshooting

**Scenario:** The accounting manager reports internet connectivity issues. Use FortiGate's comprehensive logging capabilities to diagnose and understand traffic patterns.

#### Objective
Analyze network traffic logs to understand connectivity issues and identify blocked or allowed traffic patterns.

#### Log Analysis Procedures

**Access Traffic Logs:**
1. Navigate to **Log & Report → Forward Traffic**
2. Apply source filter for accounting network: `10.0.3.2`
3. Examine traffic to DNS servers (1.1.1.1) for connectivity analysis

#### Traffic Analysis Questions

**Question 1:** In the Source section of log details, what is the source interface being detected by FortiGate?

**✅ Answer:** `port3` (LAN interface)

**Question 2:** In the Application Control section of log details, what is the service being detected by FortiGate?

**✅ Answer:** `DNS`

**Question 3:** In the Action section of log details, what is the action detected by FortiGate?

**✅ Answer:** `DENY` (traffic blocked due to missing policy)

#### Troubleshooting Insights
- **Interface Detection:** FortiGate accurately identifies traffic source interfaces
- **Service Classification:** Advanced application identification beyond port numbers
- **Action Logging:** Comprehensive logging of allow/deny decisions for forensics

---

### Exercise 3: Custom Web Filter Profile Creation

**Scenario:** Create a specialized web filtering profile for the accounting department that blocks specific cloud storage services and file sharing platforms to prevent information leakage.

#### Objective
Implement a comprehensive web filtering profile using simple URL matching to block specific websites that pose data exfiltration risks.

#### Security Requirements
Block the following high-risk services:
- `www.dropbox.com/business` - Business cloud storage
- `www.dropbox.com/individual` - Personal cloud storage  
- `onedrive.live.com` - Microsoft cloud storage
- `drive.google.com` - Google cloud storage
- `mega.nz` - Encrypted cloud storage

#### Implementation Steps

**Create Web Filter Profile:**
1. Navigate to **Security Profiles → Web Filter**
2. Click **Create New**
3. Configure profile parameters:
   - **Name:** `wf-accounting`
   - **Feature Set:** `Flow-based`
   - **FortiGuard Categories:** `Disabled` (using custom URL filtering)

**Configure URL Filtering:**
1. **Enable URL Filter:** Activate static URL filtering
2. **Create URL Entries:** For each target website:
   - **URL:** Enter specific URL (e.g., `www.dropbox.com/business`)
   - **Type:** `Simple` (exact match)
   - **Action:** `Block`
   - **Status:** `Enable`
   - **Logging:** Enable for monitoring

![Static URL Filter Configuration](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/static-url-filter-config.png)

![URL Entry Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/url-entry-creation.png)

**Repeat for All URLs:**
- Create individual entries for each specified URL
- Maintain consistent action (Block) and logging settings
- Verify each entry is properly enabled

#### Validation Steps
- **Profile Review:** Confirm all URLs are properly configured
- **Action Verification:** Ensure blocking actions are correctly set
- **Logging Configuration:** Validate logging is enabled for security monitoring

---

### Exercise 4: Advanced Wildcard Pattern Filtering

**Scenario:** Simple URL filtering can be bypassed by accessing alternate URLs on the same domain. Implement wildcard patterns to provide comprehensive domain-level blocking.

#### Objective
Enhance the web filtering profile with wildcard expressions to block entire domain categories and prevent circumvention through alternate URLs.

#### Wildcard Pattern Theory
**Wildcard Syntax:**
- `*.domain.com` - Matches any subdomain of domain.com
- `domain.com/*` - Matches any path under domain.com
- `*.domain.com/*` - Matches any subdomain and any path

#### Implementation Tasks

**Edit Existing Profile:**
1. Access the `wf-accounting` profile
2. Navigate to **URL Filter** section
3. Add wildcard entries

**Create Wildcard Entries:**

1. **Dropbox Domain Blocking:**
   - **URL:** `dropbox.com/*`
   - **Type:** `Wildcard`
   - **Action:** `Block`
   - **Purpose:** Block all Dropbox paths and services

2. **Mega Service Blocking:**
   - **URL:** `*.mega.co.nz/*`
   - **Type:** `Wildcard` 
   - **Action:** `Block`
   - **Purpose:** Block all Mega subdomains and paths

#### Security Benefits
- **Comprehensive Coverage:** Prevents circumvention through alternate URLs
- **Subdomain Protection:** Blocks all variations of target domains
- **Path Independence:** Covers all possible resource paths
- **Future-Proof:** Automatically covers new services on blocked domains

---

### Exercise 5: Regular Expression Pattern Filtering

**Scenario:** The accounting manager has identified productivity issues caused by employees accessing multiple news websites during work hours. Implement regex-based filtering to efficiently block multiple news domains.

#### Objective
Create advanced regular expression filters to block multiple news websites using a single, efficient rule that demonstrates pattern matching capabilities.

#### Regular Expression Fundamentals

**Key Regex Syntax for URL Filtering:**
- `|` - OR operator (matches either pattern)
- `*` - Matches preceding character 0 or more times
- `^` - Matches beginning of string
- `\.` - Literal dot character (escaped)
- `/i` - Case-insensitive flag

#### Target News Websites
- `cnn.com` - Cable news network
- `news.yahoo.com` - Yahoo news portal
- `foxnews.com` - Fox news network
- `nbcnews.com` - NBC news network

#### Regular Expression Implementation

**Create Regex Filter:**
1. **Edit Profile:** Access `wf-accounting` web filter profile
2. **Add Regex Entry:**
   - **URL:** `(cnn|news|foxnews|nbcnews)\.(com|yahoo\.com)`
   - **Type:** `Regular Expression`
   - **Action:** `Block`
   - **Status:** `Enable`

**Pattern Explanation:**
```
(cnn|news|foxnews|nbcnews) - Matches any of these domain prefixes
\.                          - Literal dot character
(com|yahoo\.com)           - Matches either .com or .yahoo.com
```

**Coverage Analysis:**
- `cnn.com` ✅
- `news.yahoo.com` ✅ (matches "news" + "yahoo.com")
- `foxnews.com` ✅
- `nbcnews.com` ✅

#### Advanced Pattern Benefits
- **Efficiency:** Single rule blocks multiple domains
- **Scalability:** Easy to add new patterns
- **Maintenance:** Centralized pattern management
- **Performance:** Optimized regex processing

---

### Exercise 6: Network Object and Address Group Creation

**Scenario:** Create network objects and address groups to support granular firewall policy implementation for department-specific web filtering.

#### Objective
Establish the network infrastructure objects required for applying web filtering policies to specific network segments and supporting services.

#### Network Object Requirements

**Create Accounting Network Object:**
1. Navigate to **Policy & Objects → Addresses**
2. Click **Create New → Address**
3. Configure network object:
   - **Name:** `ACCOUNTING-NETWORK`
   - **Type:** `Subnet`
   - **Subnet:** `10.0.3.0/24`
   - **Interface:** `any`
   - **Purpose:** Represents accounting department network segment

![Network Address Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/network-address-creation.png)

**Create DNS Server Objects:**

1. **Primary DNS Server:**
   - **Name:** `1.1.1.1`
   - **Type:** `Subnet`
   - **Subnet:** `1.1.1.1/32`
   - **Interface:** `any`

2. **Secondary DNS Server:**
   - **Name:** `8.8.8.8`
   - **Type:** `Subnet` 
   - **Subnet:** `8.8.8.8/32`
   - **Interface:** `any`

**Create DNS Server Group:**
1. Click **Create New → Address Group**
2. Configure group object:
   - **Group Name:** `DNS-SERVERS`
   - **Type:** `Group`
   - **Members:** `1.1.1.1`, `8.8.8.8`
   - **Purpose:** Simplifies DNS policy management

#### Object Management Benefits
- **Centralized Management:** Single location for network definitions
- **Policy Reusability:** Objects can be used across multiple policies
- **Scalability:** Easy addition of new network segments
- **Maintenance:** Simplified updates and modifications

---

### Exercise 7: Web Filtering Policy Integration

**Scenario:** Integrate the custom web filtering profile with firewall policies to enforce URL filtering controls on the accounting department network.

#### Objective
Create comprehensive firewall policies that combine network access control with web filtering to enforce corporate security policies.

#### Primary Web Filtering Policy

**Create Internet Access Policy:**
1. Navigate to **Policy & Objects → Firewall Policy**
2. Click **Create New**
3. Configure policy parameters:
   - **Name:** `INTERNET_ACCOUNTING`
   - **Incoming Interface:** `port3` (LAN)
   - **Outgoing Interface:** `port2` (WAN)
   - **Source:** `ACCOUNTING-NETWORK`
   - **Destination:** `all`
   - **Service:** `HTTP`, `HTTPS`
   - **Action:** `ACCEPT`

**Configure Security Profiles:**
1. **Enable NAT:** Use outgoing interface address
2. **Web Filter:** `Enabled`
3. **Web Filter Profile:** `wf-accounting`
4. **SSL Inspection:** `SSL certificate-inspection`

![Firewall Policy Creation](https://raw.githubusercontent.com/Mustangrim/Firewalls/main/assets/screenshots/firewall-policy-creation.png)

#### DNS Resolution Policy

**Create DNS Access Policy:**
1. **Policy Name:** `DNS_TRAFFIC`
2. **Incoming Interface:** `port3` (LAN)
3. **Outgoing Interface:** `port2` (WAN)
4. **Source:** `ACCOUNTING-NETWORK`
5. **Destination:** `DNS-SERVERS`
6. **Service:** `DNS`
7. **Action:** `ACCEPT`
8. **NAT:** Enabled with outgoing interface address

#### Policy Integration Benefits
- **Comprehensive Security:** Combines access control with content filtering
- **Granular Control:** Department-specific policies and restrictions
- **Service Continuity:** Separate DNS policy ensures name resolution
- **Audit Trail:** Complete logging for compliance and forensics

---

### Exercise 8: Policy Testing and Validation

**Scenario:** Perform comprehensive testing to validate that web filtering policies are functioning correctly and providing the intended security controls.

#### Objective
Execute systematic testing procedures to confirm policy effectiveness and troubleshoot any connectivity issues.

#### Remote Access Setup

**Connect to Test Machine:**
```bash
ssh student@desktop-accounting-1
# Username: student
# Password: student
```

#### Functionality Testing

**Test Web Filtering Effectiveness:**

1. **Test Blocked Sites:**
   ```bash
   curl -I https://www.dropbox.com/business
   curl -I https://drive.google.com
   curl -I https://cnn.com
   ```

2. **Test Allowed Sites:**
   ```bash
   curl -I https://www.microsoft.com
   curl -I https://www.google.com (search, not drive)
   ```

3. **Test DNS Resolution:**
   ```bash
   nslookup google.com
   dig @1.1.1.1 microsoft.com
   ```

#### Expected Results Analysis

**Question:** What status code did you receive when testing access to CNN.com?

**✅ Answer:** `HTTP/1.1 403 Forbidden` or connection timeout (blocked by web filter)

**Blocked Site Indicators:**
- HTTP 403 Forbidden status
- Connection timeouts
- FortiGate block page display
- No response from curl commands

**Allowed Site Indicators:**
- HTTP 200 OK status
- Successful DNS resolution
- Normal web page loading
- Proper curl response headers

---

## Advanced Web Filtering Concepts

### Security Architecture Integration

**Layered Security Approach:**
1. **Perimeter Defense:** Firewall rules control network access
2. **Content Filtering:** Web filtering blocks inappropriate content
3. **Application Control:** Layer 7 filtering for applications
4. **SSL Inspection:** Encrypted traffic analysis
5. **Threat Intelligence:** Real-time reputation and categorization

### FortiGuard Integration Benefits

**Real-Time Protection:**
- Continuous URL database updates
- Threat intelligence integration
- Zero-day malicious site protection
- Dynamic risk scoring and categorization

**Category-Based Filtering:**
- Pre-defined categories for common content types
- Productivity categories (social media, gaming, news)
- Security categories (malware, phishing, proxy)
- Compliance categories (adult content, gambling, weapons)

### Performance Optimization

**Web Filtering Best Practices:**
- **Rule Order:** Place specific rules before general ones
- **Regex Optimization:** Use efficient patterns to minimize processing
- **Caching:** Leverage FortiGate's URL caching for performance
- **SSL Inspection:** Balance security needs with performance impact

---

## Compliance and Regulatory Alignment

### ISMS Implementation Support

**Information Security Management System (ISMS) Benefits:**
- **Access Control:** Granular web access management
- **Information Classification:** Content-based filtering controls
- **Monitoring and Logging:** Comprehensive audit trails
- **Risk Management:** Proactive threat prevention

### Audit and Reporting Capabilities

**Compliance Reporting Features:**
- **Usage Reports:** Employee internet usage patterns
- **Security Events:** Blocked access attempts and policy violations
- **Trend Analysis:** Long-term security posture assessment
- **Executive Dashboards:** High-level security metrics and KPIs

---

## Troubleshooting Common Issues

### Web Filtering Implementation Challenges

**Issue 1: Legitimate Sites Blocked**
- **Symptoms:** Business-critical sites unexpectedly blocked
- **Solution:** Create allow entries or adjust category settings
- **Prevention:** Comprehensive testing before production deployment

**Issue 2: Policy Bypass**
- **Symptoms:** Users accessing blocked content through proxies
- **Solution:** Block proxy and anonymization categories
- **Prevention:** Comprehensive category-based filtering

**Issue 3: SSL/HTTPS Filtering**
- **Symptoms:** HTTPS sites not properly filtered
- **Solution:** Implement SSL inspection with appropriate certificates
- **Prevention:** Plan SSL inspection deployment with certificate management

### Log Analysis Techniques

**Effective Web Filter Log Review:**
1. **Time-based Analysis:** Focus on specific incident periods
2. **User Behavior:** Track usage patterns by department
3. **Category Trends:** Identify commonly blocked content types
4. **Policy Effectiveness:** Measure blocking vs. allowing ratios

---

## Lab Summary

This comprehensive URL Filtering lab demonstrates advanced web content filtering capabilities using FortiGate NGFW technology. The multi-layered approach combines technical security controls with business requirements to create effective, sustainable web security policies.

**Key Technical Achievements:**
- **Multi-Pattern Filtering:** Implementation of simple, wildcard, and regex filtering methods
- **Comprehensive Policy Integration:** Web filtering integrated with firewall access controls
- **Business-Aligned Security:** Department-specific policies balancing security and productivity
- **Systematic Validation:** Thorough testing and logging analysis procedures
- **Scalable Architecture:** Network objects and groups supporting organizational growth

**Real-World Applications:**
- **Enterprise Web Security:** Corporate acceptable use policy enforcement
- **Compliance Programs:** ISMS and regulatory requirement implementation
- **Productivity Management:** Balancing security controls with business needs
- **Incident Response:** Web-based threat detection and analysis
- **Risk Management:** Proactive prevention of web-based security risks

**Career Development Value:**
- **Advanced Security Skills:** Next-generation firewall web filtering expertise
- **Pattern Matching:** Regular expression and wildcard pattern development
- **Policy Integration:** Complex security policy design and implementation
- **Business Acumen:** Understanding security-productivity balance requirements
- **Analytical Capabilities:** Web traffic analysis and security event correlation
