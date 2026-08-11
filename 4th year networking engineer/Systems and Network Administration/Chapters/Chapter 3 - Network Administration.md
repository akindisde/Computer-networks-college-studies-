# Chapter 3 - Network Administration

## Learning Objectives

By the end of this chapter, you should be able to:

- Explain the roles of a **proxy**, **reverse proxy**, and **load  
    balancer**.
    
- Distinguish between forward-proxy and reverse-proxy architectures.
    
- Explain the concepts of **high availability (HA)**, redundancy,  
    failover, and single points of failure.
    
- Understand how network architecture contributes to service  
    availability and security.
    
- Explain the security objectives of systems and networks.
    
- Understand the purpose of a **DMZ (Demilitarized Zone)**.
    
- Explain firewall concepts including rules, zones, policies, stateful  
    inspection, and default-deny strategies.
    
- Understand the purpose of network and system monitoring.
    
- Identify important metrics and events to monitor.
    
- Explain the **SNMP (Simple Network Management Protocol)**  
    architecture and terminology.
    
- Distinguish between SNMP managers, agents, OIDs, MIBs, and traps.
    
- Understand the differences between SNMP versions, particularly  
    SNMPv2c and SNMPv3.
    
- Design a basic secure and highly available network architecture.
    
- Troubleshoot common proxy, firewall, availability, and monitoring  
    problems.
    

---

# 1. Introduction to Network Administration

## 1.1 What is network administration?

**Network administration** is the discipline of designing, configuring,  
operating, securing, monitoring, and troubleshooting computer networks.

A network administrator is responsible for ensuring that systems can  
communicate:

- Reliably
    
- Securely
    
- Efficiently
    
- Predictably
    
- With appropriate availability
    

Typical network-administration responsibilities include:

- IP addressing
    
- Subnetting
    
- Routing
    
- Switching
    
- VLAN configuration
    
- DNS
    
- DHCP
    
- Firewalls
    
- VPNs
    
- Proxies
    
- Load balancing
    
- Network monitoring
    
- Network security
    
- Troubleshooting
    
- Capacity planning
    

---

## 1.2 The network as a service platform

Modern organizations depend on networks for almost every IT service.

A simplified environment might look like:

```
                         Internet
                            |
                            v
                     +-------------+
                     |   Firewall  |
                     +------+------+
                            |
                      Public/DMZ
                            |
             +--------------+--------------+
             |                             |
             v                             v
       Reverse Proxy                 Public Services
             |
             v
       Load Balancer
             |
       +-----+-----+
       |           |
       v           v
   Web Server 1  Web Server 2
       |           |
       +-----+-----+
             |
             v
        Application
             |
             v
        Database
```

The network is therefore not simply a collection of cables and IP  
addresses.

It is part of the architecture that provides:

- Availability
    
- Security
    
- Performance
    
- Scalability
    
- Isolation
    
- Observability
    

---

# 2. Proxy Servers

## 2.1 What is a proxy?

A **proxy server** is an intermediary that makes requests on behalf of a  
client.

The client does not communicate directly with the destination server.

Instead:

```
Client
   |
   | Request
   v
Proxy
   |
   | Request
   v
Internet / Destination
```

The proxy is therefore positioned between the client and the external  
destination.

---

## 2.2 Forward proxy

A **forward proxy** is typically used by clients to access external  
resources.

Example:

```
Employee PC
     |
     v
Forward Proxy
     |
     v
Internet
     |
     v
Web Server
```

The proxy acts on behalf of the client.

---

## 2.3 Why use a forward proxy?

Common objectives include:

### Access control

Organizations can control which destinations users are allowed to  
access.

### Filtering

The proxy can enforce policies on websites or categories of traffic.

### Logging

The organization can record relevant connection information.

### Caching

Some proxies can cache frequently requested resources.

### Network control

The proxy can centralize outbound access.

### Privacy or source abstraction

The destination may see the proxy's address rather than the client's  
internal address, depending on the protocol and configuration.

---

## 2.4 Example

Without a proxy:

```
Client --------------------------> Internet
```

With a proxy:

```
Client --------> Proxy ---------> Internet
```

The proxy becomes a policy enforcement point.

---

# 3. Reverse Proxy

## 3.1 What is a reverse proxy?

A **reverse proxy** is an intermediary positioned in front of backend  
servers.

It accepts requests from clients and forwards them to appropriate  
backend systems.

```
Client
   |
   v
Reverse Proxy
   |
   +--------> Backend Server 1
   |
   +--------> Backend Server 2
   |
   +--------> Backend Server 3
```

The critical distinction is:

> **A forward proxy represents clients. A reverse proxy represents  
> servers.**

---

## 3.2 Why use a reverse proxy?

A reverse proxy can provide:

- Centralized TLS termination
    
- Access control
    
- Request filtering
    
- Routing
    
- Load balancing
    
- Caching
    
- Compression
    
- Logging
    
- Backend abstraction
    
- Protection of internal server addresses
    

---

## 3.3 TLS termination

Suppose users access:

```
https://example.com
```

The reverse proxy can terminate the TLS connection:

```
Client
   |
   | HTTPS
   v
Reverse Proxy
   |
   | HTTP or HTTPS
   v
Backend
```

Alternatively, encryption can remain enabled between the proxy and  
backend:

```
Client
   |
 HTTPS
   v
Reverse Proxy
   |
 HTTPS
   v
Backend
```

The appropriate design depends on security requirements and the trust  
boundary between components.

---

# 4. Reverse Proxy and Security

A reverse proxy can reduce direct exposure of backend systems.

Instead of:

```
Internet
   |
   +----> Web Server 1
   +----> Web Server 2
   +----> Web Server 3
```

Use:

```
Internet
   |
   v
Reverse Proxy
   |
   +----> Web Server 1
   +----> Web Server 2
   +----> Web Server 3
```

The firewall can restrict backend servers so that they accept  
application traffic only from authorized infrastructure.

This creates an additional security boundary.

---

# 5. Load Balancers

## 5.1 What is load balancing?

A **load balancer** distributes client requests across multiple backend  
servers.

Example:

```
                    Client Requests
                           |
                           v
                    +-------------+
                    |Load Balancer|
                    +------+------+
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          Server 1      Server 2      Server 3
```

The goal is to prevent one backend server from becoming an unnecessary  
bottleneck.

---

## 5.2 Why use load balancing?

Load balancing can provide:

- Scalability
    
- Better resource utilization
    
- Increased availability
    
- Fault isolation
    
- Maintenance flexibility
    
- Performance improvement
    

---

## 5.3 Load-balancing algorithms

Different algorithms can be used.

### Round robin

Requests are distributed sequentially:

```
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
```

### Weighted round robin

More powerful servers receive more requests.

```
Server 1 → weight 3
Server 2 → weight 2
Server 3 → weight 1
```

### Least connections

New requests are sent toward the server currently handling fewer  
connections.

### IP hash / source-based hashing

The client's source information can influence backend selection.

The appropriate algorithm depends on application behavior and  
requirements.

---

# 6. Health Checks

A load balancer should not send traffic to a failed backend.

Therefore, it performs **health checks**.

Conceptually:

```
Load Balancer
     |
     +-- Health check → Server 1 → OK
     |
     +-- Health check → Server 2 → FAILED
     |
     +-- Health check → Server 3 → OK
```

Traffic is then distributed only to healthy servers.

---

## 6.1 Liveness versus application health

A server responding to a network connection does not necessarily mean  
the application is healthy.

For example:

```
TCP port 443 → reachable
```

but:

```
Application → database unavailable
```

A sophisticated health check should test the service at an appropriate  
level.

---

# 7. Proxy, Reverse Proxy, and Load Balancer

These concepts are related but not identical.

---

Component Represents Typical direction Main purpose

---

Forward proxy Client Client → Internet Outbound access  
control

Reverse proxy Server/application Internet → Application  
backend gateway

A reverse proxy can also perform load balancing.

For example:

```
Internet
    |
    v
Reverse Proxy / Load Balancer
    |
    +----> Web 01
    +----> Web 02
    +----> Web 03
```

---

# 8. High Availability

## 8.1 What is high availability?

**High availability (HA)** is the ability of a system or service to  
remain operational for a high proportion of the required time, including  
during certain failures.

HA is not simply "having two servers."

It requires:

- Redundancy
    
- Failure detection
    
- Failover mechanisms
    
- Appropriate architecture
    
- Operational procedures
    
- Monitoring
    
- Testing
    

---

## 8.2 Availability and downtime

Availability is often expressed as a percentage.

For example:

```
Availability = uptime / total required service time × 100
```

A higher availability target means a smaller acceptable downtime budget.

The exact availability target must be based on business requirements.

---

## 8.3 Single Point of Failure

A **Single Point of Failure (SPOF)** is a component whose failure can  
cause the service to become unavailable.

Example:

```
Internet
    |
    v
Single Firewall
    |
    v
Web Servers
```

If the firewall fails:

```
Internet
    X
    |
    v
Web Servers
```

The firewall is a SPOF.

---

## 8.4 Removing the single point of failure

A redundant architecture could use:

```
                 Internet
                    |
             +------+------+
             |             |
             v             v
        Firewall A     Firewall B
             |             |
             +------+------+
                    |
             Internal Network
```

The two firewalls may operate in an HA configuration with failover.

The exact mechanism depends on the platform.

---

# 9. Redundancy

Redundancy means having additional components capable of supporting  
service operation when another component fails.

Examples:

- Two firewalls
    
- Two switches
    
- Multiple web servers
    
- Multiple power supplies
    
- Multiple network paths
    
- Multiple database replicas
    
- Multiple Internet connections
    

But redundancy alone is not enough.

If redundant components are connected to the same failed power source,  
building, or network path, they may still share a failure domain.

---

# 10. Failure Domains

A **failure domain** is a set of components that can be affected by the  
same failure.

Examples:

```
Same power supply
Same rack
Same switch
Same network path
Same data center
Same geographic region
```

A good HA architecture considers failure domains.

For example:

```
Data Center A             Data Center B

Web 01                    Web 03
Web 02                    Web 04
   |                         |
   +----------+--------------+
              |
        Load Balancing
```

Geographic redundancy can provide stronger resilience than placing every  
redundant component in one room.

---

# 11. Security of Systems and Networks

Network security protects:

- Confidentiality
    
- Integrity
    
- Availability
    

These three principles are often called the **CIA triad**.

### Confidentiality

Prevent unauthorized disclosure.

### Integrity

Prevent unauthorized modification.

### Availability

Ensure authorized users can access required services.

---

# 12. Defense in Depth

Security should not rely on one mechanism.

A defense-in-depth architecture might look like:

```
Internet
   |
   v
Edge protection
   |
   v
Firewall
   |
   v
DMZ
   |
   v
Internal firewall
   |
   v
Application network
   |
   v
Database network
```

If one control fails, additional controls remain.

---

# 13. DMZ --- Demilitarized Zone

## 13.1 What is a DMZ?

A **DMZ (Demilitarized Zone)** is a network segment designed to host  
systems that require controlled interaction with less-trusted networks,  
typically the Internet.

Examples of systems that may be placed in a DMZ include:

- Public web servers
    
- Reverse proxies
    
- Mail gateways
    
- Public DNS servers
    
- VPN gateways
    
- Other externally reachable services
    

The DMZ provides isolation between public-facing services and internal  
systems.

---

## 13.2 Basic DMZ architecture

A simple architecture is:

```
                         Internet
                            |
                            v
                    +---------------+
                    |   Firewall    |
                    +-------+-------+
                            |
                            v
                          DMZ
                    +-------+-------+
                    |               |
                    v               v
              Reverse Proxy    Public Service
                    |
                    v
             Internal Firewall
                    |
                    v
              Internal Network
```

The important idea is that DMZ systems should not automatically have  
unrestricted access to the internal network.

---

# 14. DMZ Security Policy

Suppose a public web server needs to access a database.

A poor design would be:

```
DMZ Web Server
      |
      | ANY
      v
Internal Network
```

A better approach is:

```
DMZ Web Server
      |
      | TCP/5432
      | Database destination only
      v
Database Server
```

The rule should be as specific as possible.

This follows the principle:

> **Allow required communication, deny unnecessary communication.**

---

# 15. Firewall Management

## 15.1 What is a firewall?

A **firewall** is a security control that filters network traffic  
according to defined rules or policies.

A firewall may inspect:

- Source IP
    
- Destination IP
    
- Protocol
    
- Source port
    
- Destination port
    
- Connection state
    
- Interface
    
- Application information, depending on firewall capabilities
    

---

## 15.2 Example firewall rule

A simplified rule might be:

```
Source:      Internet
Destination: Web Server
Protocol:    TCP
Port:        443
Action:      ALLOW
```

Another rule might be:

```
Source:      DMZ
Destination: Internal Network
Protocol:    Any
Action:      DENY
```

Firewall rules should be based on actual communication requirements.

---

# 16. Stateful Firewalls

A **stateful firewall** tracks the state of network connections.

For example:

```
Client → Server
TCP connection established
```

The firewall can understand that return traffic belongs to an  
established connection.

This is more sophisticated than treating each packet as completely  
independent.

---

# 17. Default Deny

A strong firewall policy often follows:

```
Allow explicitly required traffic
Deny everything else
```

Conceptually:

```
Rule 1 → Allow HTTPS to public web service
Rule 2 → Allow SSH from administration network
Rule 3 → Allow application-to-database traffic
Rule 4 → Deny all other traffic
```

This is generally safer than:

```
Allow everything
```

---

# 18. Firewall Rule Ordering

On many firewall systems, rule ordering matters.

For example:

```
Rule 1: DENY all
Rule 2: ALLOW HTTPS
```

If the firewall evaluates Rule 1 first and stops processing, HTTPS may  
never be allowed.

Correct policy processing depends on the firewall platform, but  
administrators must always understand:

- Rule order
    
- Matching behavior
    
- Default policy
    
- State tracking
    
- NAT behavior
    
- Logging
    

---

# 19. Firewall Management Best Practices

## Principle 1 --- Minimize exposure

Open only required ports.

## Principle 2 --- Restrict source networks

If SSH is needed only from the administration subnet, do not expose it  
globally.

## Principle 3 --- Use explicit policies

Document why each rule exists.

## Principle 4 --- Log important events

Logging helps with troubleshooting and security investigations.

## Principle 5 --- Review rules regularly

Unused rules increase complexity and may create unintended access.

## Principle 6 --- Back up firewall configuration

A firewall is critical infrastructure.

## Principle 7 --- Test changes carefully

A firewall error can disconnect administrators or entire network  
segments.

---

# 20. Network Monitoring

## 20.1 What is monitoring?

**Monitoring** is the continuous or periodic observation of  
infrastructure health and behavior.

Monitoring answers questions such as:

- Is the device reachable?
    
- Is CPU usage normal?
    
- Is memory available?
    
- Is disk space sufficient?
    
- Is the interface operational?
    
- Is bandwidth utilization increasing?
    
- Are errors occurring?
    
- Are services responding?
    
- Are network devices reporting failures?
    

---

# 21. Why Monitoring Matters

Without monitoring:

```
Failure
   |
   v
Users notice
   |
   v
Help desk receives complaints
   |
   v
Administrator investigates
```

With monitoring:

```
Failure
   |
   v
Monitoring detects event
   |
   v
Alert
   |
   v
Administrator investigates
```

Monitoring can therefore reduce **Mean Time to Detect (MTTD)**.

Good monitoring also helps reduce **Mean Time to Repair/Recover (MTTR)**  
by providing evidence about the failure.

---

# 22. What Should Be Monitored?

## 22.1 Availability

Examples:

- Host reachable
    
- Service reachable
    
- HTTP endpoint responding
    
- DNS responding
    
- VPN gateway operational
    

---

## 22.2 Performance

Examples:

- CPU utilization
    
- Memory utilization
    
- Disk I/O
    
- Network throughput
    
- Packet loss
    
- Latency
    

---

## 22.3 Capacity

Monitor:

- Disk usage
    
- Memory pressure
    
- Connection counts
    
- Interface utilization
    
- Database capacity
    

Capacity monitoring can identify problems before they become outages.

---

## 22.4 Errors

Examples:

- Interface errors
    
- Dropped packets
    
- Service failures
    
- Authentication failures
    
- Hardware alarms
    
- Temperature alerts
    

---

# 23. Monitoring versus Logging

These concepts are related but different.

### Monitoring

Answers:

> **Is something healthy or unhealthy?**

### Logging

Provides detailed event information:

> **What happened?**

For example:

```
Monitoring:
    nginx service = DOWN

Logs:
    Configuration syntax error at line 42
```

Monitoring identifies the problem.

Logs help explain it.

---

# 24. Monitoring Architecture

A monitoring system commonly contains:

```
                 Monitoring Server
                        |
         +--------------+--------------+
         |              |              |
         v              v              v
      Server A       Switch A       Firewall A
         |              |              |
         +--------------+--------------+
                        |
                     Alerts
                        |
                        v
                Administrator
```

Monitoring may use:

- ICMP
    
- SNMP
    
- HTTP/HTTPS
    
- SSH
    
- Agents
    
- APIs
    
- Logs
    
- Application-specific protocols
    

---

# 25. Introduction to SNMP

## 25.1 What is SNMP?

**SNMP (Simple Network Management Protocol)** is a protocol used to  
monitor and manage networked devices.

It is widely associated with:

- Switches
    
- Routers
    
- Firewalls
    
- Printers
    
- UPS systems
    
- Servers
    
- Wireless controllers
    
- Network appliances
    

SNMP allows management systems to retrieve information from devices and,  
depending on configuration and use case, receive notifications or modify  
certain managed values.

---

# 26. SNMP Architecture

A simplified SNMP environment contains:

```
                SNMP Manager
              / Monitoring NMS
                      |
                 SNMP protocol
                      |
        +-------------+-------------+
        |             |             |
        v             v             v
     Router        Switch        Firewall
     Agent         Agent          Agent
```

The main components are:

### SNMP Manager

The management system that requests or receives information.

Often part of a **Network Management System (NMS)**.

### SNMP Agent

Software running on the managed device.

It collects or exposes management information.

### Managed Device

The network device or server being monitored.

---

# 27. SNMP Manager

The manager is responsible for:

- Polling devices
    
- Collecting metrics
    
- Receiving notifications
    
- Storing monitoring information
    
- Generating alerts
    
- Presenting dashboards
    

For example:

```
NMS
 |
 | "What is interface utilization?"
 v
Router
 |
 | "Interface utilization = X"
 v
NMS
```

---

# 28. SNMP Agent

The **SNMP agent** runs on the managed device.

It provides access to management information.

For example, a switch agent may expose:

- Interface status
    
- Interface counters
    
- CPU utilization
    
- Memory utilization
    
- Device information
    
- Error counters
    

---

# 29. MIB --- Management Information Base

A **MIB (Management Information Base)** describes the structure of  
managed information available through SNMP.

Think of a MIB as a conceptual dictionary describing manageable objects.

Examples:

```
Device name
Interface status
Interface byte counters
CPU usage
Memory usage
```

A MIB is not simply a database file containing all current values. It  
defines managed objects and their structure.

---

# 30. OID --- Object Identifier

An **OID (Object Identifier)** uniquely identifies a managed object in  
the SNMP object hierarchy.

The hierarchy is represented as a numeric tree.

Conceptually:

```
1
└── 3
    └── 6
        └── 1
            └── ...
```

An OID may look like:

```
1.3.6.1....
```

An SNMP manager uses OIDs to identify the information it wants to  
retrieve.

---

# 31. SNMP Operations

Common SNMP operations include:

### GET

The manager requests a value.

```
Manager → GET → Agent
Manager ← value ← Agent
```

### GETNEXT

Used to retrieve the next object in an OID hierarchy.

### GETBULK

Used to efficiently retrieve multiple values, particularly in SNMPv2 and  
later.

### SET

The manager requests a change to a managed object when the device and  
object permit it.

### TRAP

The agent sends an unsolicited notification to the manager.

### INFORM

Similar to a notification but provides acknowledgment behavior between  
SNMP entities.

---

# 32. Polling

A common monitoring pattern is periodic polling.

```
Every 60 seconds:

NMS → Switch → CPU utilization
NMS → Switch → Interface status
NMS → Switch → Interface traffic
NMS → Switch → Error counters
```

This allows the monitoring system to build historical data.

---

# 33. SNMP Traps

Polling is not always sufficient.

A device may immediately send a notification when an important event  
occurs.

Example:

```
Interface failure
      |
      v
SNMP Agent
      |
      | TRAP
      v
Monitoring System
      |
      v
Alert
```

This is useful for events such as:

- Link failure
    
- Hardware fault
    
- Power event
    
- Temperature alarm
    
- Authentication event
    
- Device reboot
    

---

# 34. Polling versus Traps

Mechanism Direction Purpose

---

Polling / GET Manager → Agent Periodically retrieve information  
TRAP Agent → Manager Notify manager of an event  
INFORM Agent → Manager Notification with acknowledgment

A monitoring system often uses both polling and notifications.

---

# 35. SNMP Versions

The major versions encountered in network administration include:

- SNMPv1
    
- SNMPv2c
    
- SNMPv3
    

## SNMPv1

An older version with basic functionality.

## SNMPv2c

Provides improvements over SNMPv1, including operations such as GETBULK.

However, the `c` in v2c refers to **community-based security**, which  
does not provide modern cryptographic protection.

## SNMPv3

Adds security mechanisms including:

- Authentication
    
- Integrity protection
    
- Privacy/encryption, depending on security level
    

For security-sensitive environments, SNMPv3 is generally preferred when  
supported and appropriately configured.

---

# 36. SNMP Community Strings

SNMPv1 and SNMPv2c commonly use **community strings**.

They are often described as a kind of shared credential or access token.

For example:

```
Community: monitoring
```

A poorly configured community string can expose management information.

Do not treat a community string as equivalent to strong modern  
cryptographic authentication.

Security-conscious deployments should:

- Restrict source IPs
    
- Use appropriate access controls
    
- Avoid default community strings
    
- Prefer SNMPv3 where practical
    

---

# 37. SNMPv3 Security Levels

SNMPv3 supports different security levels.

Conceptually:

```
noAuthNoPriv
    ↓
Authentication without privacy
    ↓
Authentication + privacy
```

The highest common security model provides:

- Authentication
    
- Integrity
    
- Encryption/privacy
    

The exact configuration should match the organization's security  
requirements.

---

# 38. SNMP Ports

SNMP traditionally uses:

```
UDP/161 → SNMP queries
UDP/162 → SNMP notifications such as traps
```

Firewalls must permit only the required traffic between authorized  
monitoring systems and managed devices.

For example:

```
NMS → UDP/161 → Network Devices
Network Devices → UDP/162 → NMS
```

Do not expose SNMP management access broadly to untrusted networks.

---

# 39. SNMP and Network Monitoring

SNMP is particularly useful for collecting time-series information.

For example:

```
Interface traffic

Time
 |
 |       /\
 |      /  \      /\
 |  ___/    \____/  \____
 |
 +------------------------>
```

The monitoring platform can use these values to identify:

- Traffic trends
    
- Congestion
    
- Capacity issues
    
- Interface failures
    
- Abnormal behavior
    

---

# 40. Monitoring a Switch with SNMP

Suppose a switch has 24 ports.

The monitoring system can collect:

```
Port 1:
    Status = up
    RX bytes = ...
    TX bytes = ...
    Errors = ...

Port 2:
    Status = down
    RX bytes = ...
    TX bytes = ...
    Errors = ...
```

Over time, the monitoring system can calculate traffic rates and detect  
anomalies.

---

# 41. Monitoring Network Capacity

Suppose an uplink is:

```
1 Gbit/s
```

Monitoring shows sustained utilization near:

```
900 Mbit/s
```

This may indicate a capacity problem.

Without historical monitoring:

```
Users complain about slow network
       ↓
Administrator investigates
```

With monitoring:

```
Utilization trend increases
       ↓
Alert / capacity report
       ↓
Upgrade planned before outage
```

Monitoring is therefore not only about detecting failures.

It supports **capacity planning**.

---

# 42. Security Monitoring

Monitoring can also detect suspicious activity.

Examples:

- Unexpected configuration changes
    
- Repeated authentication failures
    
- Unusual traffic
    
- Unexpected interface state changes
    
- High outbound traffic
    
- Device reboots
    
- Firewall rule changes
    

Monitoring is not a replacement for a dedicated security-monitoring  
system, but it is an important part of operational security.

---

# 43. Network Monitoring and Alert Fatigue

A poorly designed monitoring system can produce too many alerts.

Example:

```
1000 alerts/day
        ↓
Administrator ignores alerts
        ↓
Critical event is missed
```

Good monitoring requires:

- Appropriate thresholds
    
- Alert prioritization
    
- Dependency awareness
    
- Suppression of duplicate alerts
    
- Escalation policies
    
- Meaningful notification channels
    

The goal is not:

> **Generate as many alerts as possible.**

The goal is:

> **Generate actionable alerts.**

---

# 44. Integrated Architecture

The concepts from this chapter can be combined.

Consider:

```
                              Internet
                                  |
                                  v
                         +----------------+
                         | Edge Firewall  |
                         +-------+--------+
                                 |
                                DMZ
                                 |
                         +-------+-------+
                         |               |
                         v               v
                  Reverse Proxy     VPN Gateway
                         |
                         v
                  Load Balancer
                         |
                +--------+--------+
                |        |        |
                v        v        v
              Web01    Web02    Web03
                |        |        |
                +--------+--------+
                         |
                         v
                    App Network
                         |
                         v
                    DB Cluster
```

Monitoring:

```
                    Monitoring / NMS
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
       Firewall        Switches         Servers
          |                |                |
          +----------------+----------------+
                           |
                         SNMP
```

This architecture combines:

- Firewalling
    
- DMZ segmentation
    
- Reverse proxy
    
- Load balancing
    
- High availability
    
- Network monitoring
    
- SNMP
    

---

# 45. Practical Troubleshooting Methodology

When a network service fails, troubleshoot from evidence rather than  
assumptions.

## Layer 1 --- Is the host reachable?

Check:

- IP configuration
    
- Routing
    
- Interface state
    
- Packet loss
    

## Layer 2 --- Is the required port reachable?

Check:

- Firewall
    
- Listening socket
    
- Network ACL
    
- Security policy
    

## Layer 3 --- Is the proxy/load balancer healthy?

Check:

- Frontend listener
    
- Backend pool
    
- Health checks
    
- TLS certificates
    
- Routing rules
    

## Layer 4 --- Is the backend service healthy?

Check:

- Service state
    
- Application logs
    
- Resource usage
    
- Database dependencies
    

## Layer 5 --- Is the application functional?

Check:

- HTTP status
    
- Application errors
    
- Authentication
    
- Database queries
    
- External dependencies
    

---

# 46. Example Troubleshooting Scenario

A website is unavailable.

The architecture is:

```
Internet
   |
Firewall
   |
Reverse Proxy
   |
Load Balancer
   |
+-- Web01
+-- Web02
+-- Web03
```

A user reports:

> "The website does not load."

Do not immediately restart every server.

Investigate systematically.

### Step 1 --- DNS

Does the hostname resolve to the correct public address?

### Step 2 --- Firewall

Is HTTPS allowed?

### Step 3 --- Reverse proxy

Is the proxy reachable?

### Step 4 --- Load balancer

Are backend health checks passing?

### Step 5 --- Web servers

Are Web01, Web02, and Web03 running?

### Step 6 --- Application

Are the web application and database healthy?

This avoids causing additional failures while troubleshooting.

---

# 47. Practical Lab --- Build a Small Network Architecture

## Lab objectives

Build a laboratory network containing:

```
                  Client
                    |
                    v
                Firewall
                    |
                   DMZ
                    |
              Reverse Proxy
                    |
               Web Server
```

The objective is to understand network segmentation rather than to build  
a production-ready security architecture.

---

## Part A --- Define the networks

Create conceptual networks such as:

```
External network
DMZ network
Internal network
```

Example addressing:

```
External: 192.0.2.0/24
DMZ:      198.51.100.0/24
Internal: 10.10.0.0/24
```

These documentation ranges are used here as examples.

---

# 48. Lab --- Firewall Rules

Design rules that allow:

```
Internet → Reverse Proxy → HTTPS
```

And:

```
Reverse Proxy → Web Server → HTTP/HTTPS as required
```

But deny:

```
Internet → Internal Network
```

and unnecessary:

```
DMZ → Internal Network → Any
```

Create a table:

---

Source Destination Protocol Port Action Reason

---

Internet Reverse Proxy TCP 443 Allow Public HTTPS

Reverse Web Server TCP 443/80 Allow Backend  
Proxy application

Internet Internal Any Any Deny Isolation

Adjust the actual rules according to the application architecture.

---

# 49. Lab --- Reverse Proxy

Deploy a reverse proxy in front of a test web server.

Verify:

```
Client
  |
  v
Reverse Proxy
  |
  v
Web Server
```

Test:

1. Client can access the proxy.
    
2. Proxy can reach the backend.
    
3. Backend is not unnecessarily exposed directly.
    
4. Logs show requests.
    
5. A backend failure is observable.
    

---

# 50. Lab --- Load Balancing

Deploy two or more test web servers.

Architecture:

```
                 Client
                    |
                    v
              Load Balancer
                /       \
               v         v
            Web01      Web02
```

Test:

1. Both servers are healthy.
    
2. Requests reach both servers.
    
3. Stop Web01.
    
4. Verify Web02 continues serving requests.
    
5. Restore Web01.
    
6. Verify it returns to the backend pool.
    

This demonstrates the relationship between:

- Load balancing
    
- Health checks
    
- Redundancy
    
- Availability
    

---

# 51. Lab --- SNMP

Set up:

```
Monitoring Server
       |
       | SNMP
       v
Test Network Device
```

Configure an SNMP agent on a laboratory device or server where  
supported.

Your monitoring system should retrieve values such as:

- System information
    
- Interface status
    
- Interface counters
    
- CPU utilization, where available
    
- Memory information, where available
    

Record:

```
Device:
SNMP version:
Manager:
Agent:
OIDs:
Polling interval:
```

---

# 52. Lab --- SNMP Notifications

If your laboratory equipment supports it, configure a test notification.

For example:

```
Interface changes state
        ↓
SNMP Agent
        ↓
TRAP / notification
        ↓
Monitoring System
        ↓
Alert
```

Test by intentionally changing a non-critical test interface.

Do not perform disruptive tests on production equipment.

---

# 53. Lab --- Monitoring and Capacity Planning

Monitor a network interface over time.

Collect:

- Incoming traffic
    
- Outgoing traffic
    
- Errors
    
- Discards
    
- Interface status
    

Then answer:

1. What is the average utilization?
    
2. What is the peak utilization?
    
3. Are errors increasing?
    
4. Is the link approaching its capacity?
    
5. Should an upgrade be considered?
    

This turns monitoring data into an operational decision.

---

# 54. Exercises

## Exercise 1 --- Proxy Concepts

Explain the difference between:

1. Forward proxy
    
2. Reverse proxy
    
3. Load balancer
    

Draw a diagram for each.

---

## Exercise 2 --- High Availability

Identify the SPOFs in this architecture:

```
Internet
   |
Firewall
   |
Switch
   |
Load Balancer
   |
+-- Web01
+-- Web02
+-- Web03
```

Then propose an HA design.

---

## Exercise 3 --- DMZ

A company hosts a public website and an internal database.

Design a DMZ architecture.

Your design must ensure:

- Public users can access the website.
    
- The website can access the database only on the required port.
    
- Internet users cannot directly access the database.
    
- Internal users are protected from unnecessary DMZ traffic.
    

---

## Exercise 4 --- Firewall

Create firewall rules for:

```
Public HTTPS
Administration SSH
Application → Database
Monitoring → SNMP
```

For each rule, specify:

- Source
    
- Destination
    
- Protocol
    
- Port
    
- Action
    
- Reason
    

---

## Exercise 5 --- SNMP

Explain:

1. SNMP Manager
    
2. SNMP Agent
    
3. MIB
    
4. OID
    
5. GET
    
6. GETBULK
    
7. SET
    
8. TRAP
    
9. INFORM
    
10. SNMPv3
    

---

# 55. Knowledge Check

Answer these questions without consulting the chapter.

1. What is a forward proxy?
    
2. What is a reverse proxy?
    
3. What is the main difference between them?
    
4. What is a load balancer?
    
5. Why are health checks important?
    
6. What is high availability?
    
7. What is a Single Point of Failure?
    
8. What is redundancy?
    
9. What is a failure domain?
    
10. Explain the CIA triad.
    
11. What is defense in depth?
    
12. What is a DMZ?
    
13. Why should a DMZ not have unrestricted access to an internal  
    network?
    
14. What is a firewall?
    
15. What does "default deny" mean?
    
16. What is stateful firewall inspection?
    
17. Why does firewall rule ordering matter?
    
18. What is network monitoring?
    
19. What is the difference between monitoring and logging?
    
20. What is SNMP?
    
21. What is an SNMP manager?
    
22. What is an SNMP agent?
    
23. What is a MIB?
    
24. What is an OID?
    
25. What is the difference between polling and a trap?
    
26. Why is SNMPv3 generally preferable to SNMPv2c for security-sensitive  
    management?
    
27. What are UDP/161 and UDP/162 commonly used for?
    
28. Why is alert fatigue dangerous?
    
29. Design a secure and highly available architecture for a public web  
    application.
    

---

# 56. Mentor Challenge --- Design a Secure, Monitored, Highly Available Web Service

You are responsible for designing infrastructure for a company website.

The requirements are:

- The website must be publicly accessible.
    
- The application has three web servers.
    
- The database must not be publicly accessible.
    
- The infrastructure must tolerate the failure of one web server.
    
- Network devices must be monitored.
    
- Firewall events must be logged.
    
- Administrators need controlled remote access.
    
- The infrastructure should have no obvious unnecessary exposure.
    

Design the architecture using:

- Internet edge
    
- Firewall
    
- DMZ
    
- Reverse proxy
    
- Load balancer
    
- Web servers
    
- Application network
    
- Database
    
- Monitoring server
    
- SNMP
    

Your final diagram should resemble:

```
                         INTERNET
                             |
                             v
                    +----------------+
                    |   HA Firewall  |
                    +-------+--------+
                            |
                           DMZ
                            |
                    +-------+-------+
                    |               |
                    v               v
              Reverse Proxy     Admin/VPN
                    |
                    v
              Load Balancer
                    |
          +---------+---------+
          |         |         |
          v         v         v
        Web01     Web02     Web03
          |         |         |
          +---------+---------+
                    |
              Application Tier
                    |
                    v
              Database Tier
```

Monitoring should operate separately:

```
             Monitoring / NMS
                    |
       +------------+------------+
       |            |            |
       v            v            v
    Firewall     Switches      Servers
       |            |            |
       +------------+------------+
                    |
                   SNMP
```

Now answer:

1. Where should the DMZ be located?
    
2. Which systems should be publicly reachable?
    
3. Which traffic should the firewall allow?
    
4. What are the likely SPOFs?
    
5. How would you remove them?
    
6. What should the load balancer monitor?
    
7. What should be monitored with SNMP?
    
8. Where should SNMP traffic be allowed?
    
9. Which SNMP version would you select and why?
    
10. How would you detect a failed web server?
    
11. How would you prevent the database from being directly exposed?
    
12. What would happen if one web server failed?
    
13. What would happen if one firewall failed?
    
14. How would you monitor capacity before it becomes an outage?
    

---

# 57. Final Mental Model

The concepts in this chapter can be connected as follows:

```
                    NETWORK ADMINISTRATION
                              |
        +---------------------+---------------------+
        |                     |                     |
        v                     v                     v
     Security             Availability          Monitoring
        |                     |                     |
        v                     v                     v
      Firewall            Redundancy              SNMP
        |                     |                     |
        v                     v                     v
       DMZ              Load Balancing          Polling
        |                     |                     |
        v                     v                     v
 Reverse Proxy            Health Checks           Traps
        |
        v
   Application
```

A professional network administrator does not treat these as isolated  
technologies.

They form one system.

A reverse proxy can protect and route application traffic.

A load balancer can distribute that traffic.

High availability can prevent a single component failure from causing an  
outage.

A DMZ can isolate public-facing systems.

Firewalls can control permitted communication.

Monitoring can detect failures and capacity problems.

SNMP can provide structured management information from network devices.

The ultimate objective is not simply to configure network equipment.

It is to build an infrastructure that is:

- **Secure**
    
- **Available**
    
- **Observable**
    
- **Maintainable**
    
- **Scalable**
    
- **Predictable**
    

> **A good network administrator designs not only for normal operation,  
> but also for failure, attack, and recovery.**