# Chapter 1 - Introduction to Systems and Network Administration

## Learning Objectives

By the end of this chapter, you should be able to:

- Define **system administration** and **network administration** and  
    explain how they interact.
    
- Identify the main responsibilities of a systems and network  
    administrator.
    
- Explain the difference between a workstation operating system and a  
    **server operating system (Server OS)**.
    
- Understand the concept of **server roles** and why roles are used to  
    organize server functionality.
    
- Identify common server roles such as web, DNS, DHCP, file,  
    directory, database, and authentication services.
    
- Explain the basic administration workflow: **plan → deploy →  
    configure → monitor → maintain → secure → troubleshoot → document**.
    
- Understand the purpose and architecture of **Webmin** and  
    **Cockpit**.
    
- Distinguish between graphical administration tools and command-line  
    administration.
    
- Perform basic administrative reasoning using Webmin and Cockpit  
    without treating the GUI as a replacement for fundamental system  
    knowledge.
    

---

# 1. Introduction to Systems and Network Administration

## 1.1 What is systems administration?

**Systems administration** is the discipline of installing, configuring,  
operating, maintaining, securing, monitoring, and troubleshooting  
computer systems.

A system administrator, commonly called a **sysadmin**, is responsible  
for keeping computing infrastructure:

- **Available** --- services should be accessible when users need  
    them.
    
- **Reliable** --- systems should behave predictably.
    
- **Secure** --- systems and data must be protected against  
    unauthorized access and abuse.
    
- **Performant** --- resources should be used efficiently.
    
- **Maintainable** --- systems should be easy to update, troubleshoot,  
    and operate.
    
- **Recoverable** --- failures should not result in unacceptable data  
    or service loss.
    

Systems administration applies to physical servers, virtual machines,  
cloud instances, storage systems, operating systems, applications,  
identity systems, and many other infrastructure components.

### A useful definition

> **Systems administration is the operational management of computing  
> resources throughout their lifecycle.**

The lifecycle generally includes:

1. Planning
    
2. Installation or provisioning
    
3. Configuration
    
4. Deployment
    
5. Monitoring
    
6. Maintenance
    
7. Security management
    
8. Troubleshooting
    
9. Backup and recovery
    
10. Decommissioning
    

A good administrator does not simply "fix computers." They build and  
operate an environment in which failures are less likely, easier to  
detect, and easier to recover from.

---

## 1.2 What is network administration?

**Network administration** is the management of the infrastructure that  
allows systems to communicate.

A network administrator may be responsible for:

- IP addressing
    
- Subnetting
    
- Routing
    
- Switching
    
- DNS
    
- DHCP
    
- VLANs
    
- Firewalls
    
- VPNs
    
- Wireless networks
    
- Network monitoring
    
- Network security
    
- Connectivity troubleshooting
    

Network administration and system administration overlap significantly.

For example, a web server can be perfectly configured at the  
operating-system level but still be unreachable because:

- its IP address is incorrect;
    
- the default gateway is wrong;
    
- DNS is misconfigured;
    
- a firewall blocks the service;
    
- a switch port is incorrectly configured;
    
- the service is listening only on `localhost`.
    

This is why administrators need to understand both the **host** and the  
**network**.

---

## 1.3 Systems and networks form one infrastructure

Consider a simple web application:

```
User
  |
  | HTTPS
  v
Network
  |
  v
Firewall
  |
  v
Web Server
  |
  v
Application
  |
  v
Database Server
```

A failure at any layer can affect the application.

For example:

Layer Possible problem

---

Client Incorrect DNS cache or browser configuration  
Network Routing failure  
Firewall TCP/443 blocked  
Server OS Disk full  
Web server Service stopped  
Application Configuration error  
Database Database unavailable

Therefore, administration requires a **layered troubleshooting  
approach** rather than guessing.

---

# 2. Responsibilities of an Administrator

A systems and network administrator commonly performs the following  
activities.

## 2.1 Installation and provisioning

The administrator installs or provisions:

- Operating systems
    
- Virtual machines
    
- Applications
    
- Network services
    
- Storage
    
- User accounts
    
- Security controls
    

Modern environments increasingly use automation and  
infrastructure-as-code, but the underlying concepts remain the same.

---

## 2.2 Configuration

Configuration includes parameters such as:

- Hostname
    
- IP configuration
    
- DNS configuration
    
- User and group membership
    
- Storage
    
- Services
    
- Firewall rules
    
- Authentication
    
- Logging
    
- Time synchronization
    

Configuration should be **intentional and documented**.

---

## 2.3 User and identity management

Administrators manage identities and permissions.

Typical tasks include:

- Creating and disabling accounts
    
- Managing groups
    
- Applying permissions
    
- Managing authentication
    
- Enforcing password policies
    
- Managing administrative privileges
    
- Integrating systems with directory services
    

A fundamental security principle is **least privilege**:

> A user or service should have only the permissions required to perform  
> its legitimate task.

---

## 2.4 Service management

A server normally provides one or more services.

Examples:

- Web service
    
- DNS service
    
- DHCP service
    
- SSH service
    
- File sharing service
    
- Database service
    
- Directory service
    

Administrators must know how to:

- Start a service
    
- Stop a service
    
- Restart a service
    
- Enable it at boot
    
- Check its status
    
- Read its logs
    
- Verify that it is reachable
    

---

## 2.5 Monitoring

Monitoring answers questions such as:

- Is the server online?
    
- Is CPU usage unusually high?
    
- Is memory exhausted?
    
- Is disk space running low?
    
- Are services available?
    
- Are network interfaces healthy?
    
- Are errors increasing?
    
- Are suspicious authentication attempts occurring?
    

Monitoring changes administration from **reactive** work to  
**proactive** operations.

---

## 2.6 Backup and recovery

A backup is not useful merely because it exists.

An administrator must also know:

- What is backed up?
    
- How frequently?
    
- Where is it stored?
    
- How long is it retained?
    
- Is it protected against unauthorized access?
    
- Can it actually be restored?
    

The critical operational rule is:

> **A backup that has never been tested for restoration should not  
> automatically be considered a reliable recovery mechanism.**

---

## 2.7 Security

Security is not a separate task performed once.

It is continuous.

Typical controls include:

- Regular updates
    
- Strong authentication
    
- Access control
    
- Firewalls
    
- Secure configuration
    
- Logging
    
- Monitoring
    
- Vulnerability management
    
- Backup protection
    
- Network segmentation
    

A secure server is not necessarily a server with the most restrictive  
configuration. It is a system whose security controls are appropriate  
for its role, exposure, data, and operational requirements.

---

# 3. The Server Operating System

## 3.1 What is a server operating system?

A **server operating system (Server OS)** is an operating system  
designed to support server workloads and provide services to other  
systems or users.

Examples include:

- Linux distributions such as Debian, Ubuntu Server, Rocky Linux, and  
    Red Hat Enterprise Linux
    
- Microsoft Windows Server
    

The important distinction is not simply the graphical appearance of the  
operating system.

A server OS is used to operate workloads that may need:

- High availability
    
- Multi-user access
    
- Network services
    
- Remote administration
    
- Strong access control
    
- Resource management
    
- Centralized logging
    
- Service management
    
- Long-term maintenance
    

---

## 3.2 Server OS versus desktop OS

A desktop OS is primarily optimized for interactive use by a person  
sitting at the computer.

A server OS is generally optimized for providing services.

Characteristic Desktop OS Server OS

---

Main purpose Interactive user computing Providing services  
Typical interface GUI-oriented Often CLI-oriented, GUI optional  
Remote administration Important Essential  
Services Usually client-oriented Often service-oriented  
Multi-user operation Supported Core operational requirement  
Long-term uptime Important Usually critical  
Automation Useful Highly important  
Resource allocation User applications Services and workloads

This is a conceptual distinction, not an absolute technical boundary.  
Modern Linux systems, for example, can be configured as desktops or  
servers depending on installed software and intended use.

---

# 4. The Concept of Server Roles

## 4.1 What is a server role?

A **server role** describes the principal function or service a server  
performs for clients or other systems.

Examples include:

- Web server
    
- DNS server
    
- DHCP server
    
- File server
    
- Database server
    
- Mail server
    
- Directory server
    
- Authentication server
    
- Proxy server
    
- Virtualization server
    

The role is a **logical description of the server's purpose**.

For example:

```
Server A
├── Operating System
├── Network configuration
├── Web server software
└── Web role
```

A server can have one role or several roles.

For small environments:

```
                    +----------------------+
                    |      Server 01       |
                    |----------------------|
                    | DNS                  |
                    | DHCP                 |
                    | File sharing         |
                    | Web service          |
                    +----------------------+
```

In larger environments, roles are often separated:

```
+-------------+    +-------------+    +-------------+
| DNS Server  |    | Web Server  |    | DB Server   |
| DNS role    |    | Web role    |    | DB role     |
+-------------+    +-------------+    +-------------+
```

Role separation can improve security, performance, fault isolation, and  
maintainability.

---

## 4.2 Common server roles

### Web server

Provides HTTP/HTTPS resources to clients.

Examples of web-server software:

- Apache HTTP Server
    
- Nginx
    
- Microsoft IIS
    

Typical ports:

- HTTP: TCP/80
    
- HTTPS: TCP/443
    

---

### DNS server

The **Domain Name System (DNS)** translates names into information such  
as IP addresses.

For example:

```
www.example.com
       |
       | DNS query
       v
192.0.2.10
```

DNS is essential because users and applications generally work with  
names rather than manually entering IP addresses.

---

### DHCP server

The **Dynamic Host Configuration Protocol (DHCP)** automatically  
provides network configuration to clients.

A DHCP server may provide:

- IP address
    
- Subnet mask
    
- Default gateway
    
- DNS server addresses
    
- Lease duration
    

A simplified exchange is:

```
Client                  DHCP Server
  |                         |
  | DHCPDISCOVER            |
  |------------------------>|
  |                         |
  | DHCPOFFER               |
  |<------------------------|
  |                         |
  | DHCPREQUEST             |
  |------------------------>|
  |                         |
  | DHCPACK                 |
  |<------------------------|
```

---

### File server

A file server provides shared storage over a network.

Common technologies include:

- SMB/CIFS
    
- NFS
    

Administrators must consider:

- Permissions
    
- Authentication
    
- Storage capacity
    
- Availability
    
- Backup
    
- Data protection
    

---

### Database server

A database server provides structured data storage and database  
services.

Examples include:

- PostgreSQL
    
- MySQL
    
- MariaDB
    
- Microsoft SQL Server
    

Applications commonly communicate with database servers over a network.

---

### Directory and authentication server

A directory service centralizes identity and organizational information.

It may manage:

- Users
    
- Groups
    
- Computers
    
- Organizational units
    
- Authentication information
    
- Access policies
    

Examples include LDAP-based environments and Microsoft Active Directory.

---

## 4.3 Why roles matter

The role concept helps administrators answer:

> **What is this server supposed to do?**

This is important for:

### Security

Only necessary services should be exposed.

### Troubleshooting

Knowing the expected role helps identify abnormal behavior.

### Capacity planning

A database server and a DNS server have different resource profiles.

### Documentation

Infrastructure documentation can describe systems in terms of their  
responsibilities.

### Change management

Changes can be evaluated according to their effect on a specific role.

---

# 5. Server Administration Workflow

A professional administrator should follow a repeatable workflow.

## 5.1 Plan

Before changing a server, identify:

- Objective
    
- Scope
    
- Dependencies
    
- Risks
    
- Required resources
    
- Expected downtime
    
- Rollback strategy
    

---

## 5.2 Deploy

Install or provision the server.

This may involve:

- Physical installation
    
- Virtual machine creation
    
- Cloud instance provisioning
    
- OS installation
    
- Network configuration
    

---

## 5.3 Configure

Configure:

- Hostname
    
- Network
    
- Users
    
- Storage
    
- Services
    
- Firewall
    
- Logging
    
- Time synchronization
    

---

## 5.4 Validate

Do not assume configuration succeeded.

Verify:

- Service status
    
- Network connectivity
    
- Listening ports
    
- DNS resolution
    
- Authentication
    
- Application functionality
    

---

## 5.5 Monitor

After deployment, monitor the system for:

- Availability
    
- Performance
    
- Capacity
    
- Errors
    
- Security events
    

---

## 5.6 Maintain

Maintenance includes:

- Security updates
    
- Software updates
    
- Configuration changes
    
- Certificate renewal
    
- Log management
    
- Backup verification
    
- Capacity management
    

---

## 5.7 Document

Documentation should answer:

- What was changed?
    
- Why was it changed?
    
- When was it changed?
    
- Who changed it?
    
- What configuration is expected?
    
- How can it be restored?
    

Documentation is part of administration, not administrative overhead.

---

# 6. Graphical Administration Tools

## 6.1 Why use graphical administration tools?

Server administration is traditionally performed extensively from the  
command line.

For Linux systems, examples include commands such as:

```
systemctl
journalctl
ip
ss
df
du
free
ps
top
```

Graphical tools provide an alternative interface.

A GUI can help administrators:

- Understand system state visually
    
- Discover configuration options
    
- Perform common tasks more quickly
    
- Manage systems remotely
    
- Reduce the learning curve for certain operations
    

However:

> **A GUI does not eliminate the need to understand the underlying  
> operating system.**

If a graphical tool changes a service configuration, the administrator  
should understand what configuration was changed and why.

---

# 7. Webmin

## 7.1 What is Webmin?

**Webmin** is a web-based administration interface for Unix-like  
systems.

It allows administrators to manage many operating-system and service  
configuration tasks through a browser.

The general architecture is:

```
Administrator
      |
      | HTTPS / browser
      v
+------------------+
| Webmin interface |
+------------------+
      |
      v
+------------------+
| Linux / Unix OS  |
+------------------+
      |
      +---- Services
      +---- Users
      +---- Storage
      +---- Network
      +---- Configuration
```

Webmin provides a browser-based control plane over many administrative  
tasks.

---

## 7.2 Typical Webmin capabilities

Depending on the system and installed modules, Webmin can be used to  
manage areas such as:

- Users and groups
    
- Filesystems
    
- Disk usage
    
- Network configuration
    
- System services
    
- Scheduled tasks
    
- Software packages
    
- Firewall configuration
    
- Web and database services
    
- System logs
    

Webmin is modular, meaning its functionality is organized around  
modules.

---

## 7.3 Webmin strengths

Webmin is particularly useful for:

- Learning Linux administration
    
- Performing common configuration tasks through a browser
    
- Managing multiple configuration areas from a unified interface
    
- Environments where administrators prefer a web interface
    

It can also help students connect conceptual knowledge to practical  
administration.

---

## 7.4 Webmin limitations

A graphical tool should not be treated as an abstraction that makes  
technical knowledge unnecessary.

Potential limitations include:

- A GUI may hide important implementation details.
    
- The interface can make configuration appear simpler than it really  
    is.
    
- A GUI cannot automatically determine whether a configuration is  
    architecturally appropriate.
    
- Different distributions and software versions may expose different  
    options.
    
- Administrators still need to understand logs, services, networking,  
    permissions, and configuration files.
    

A useful learning rule is:

> **Use the GUI to perform the task, then understand the underlying  
> system change.**

---

# 8. Cockpit

## 8.1 What is Cockpit?

**Cockpit** is a web-based interface for managing and monitoring Linux  
servers.

It provides a browser-accessible dashboard for common administrative  
operations.

A simplified model is:

```
Administrator
      |
      | Web browser
      v
+--------------------+
| Cockpit web UI     |
+--------------------+
      |
      v
+--------------------+
| Linux server       |
+--------------------+
 |       |       |
 v       v       v
System  Logs   Services
       Network Storage
```

Cockpit is designed to integrate closely with the existing Linux system  
rather than replacing the underlying administration mechanisms.

---

## 8.2 Typical Cockpit capabilities

Depending on the operating system and installed components, Cockpit can  
provide visibility and administration for:

- CPU and memory utilization
    
- Storage
    
- Network interfaces
    
- System logs
    
- Services
    
- Users
    
- Software updates
    
- Virtual machines
    
- Containers
    
- System configuration
    

Cockpit is especially useful for obtaining a quick operational overview.

---

## 8.3 Cockpit and systemd

On Linux systems using **systemd**, Cockpit can expose information about  
services managed by systemd.

For example, an administrator may need to determine:

```
Is the web service running?
        |
        +-- Yes → Is it listening on the expected port?
        |
        +-- No  → Check status and logs
```

The graphical interface may make this workflow easier to visualize, but  
the underlying concepts remain:

- Service state
    
- Process state
    
- Logs
    
- Network sockets
    
- Dependencies
    

---

# 9. Webmin vs Cockpit

Webmin and Cockpit are both web-based administration tools, but they  
should not be considered identical.

---

Feature Webmin Cockpit

---

Main purpose Broad Unix/Linux Linux server management  
administration and monitoring

Interface Web GUI Web GUI

Architecture Modular administration Integrates with  
interface existing Linux  
management components

System monitoring Available Strong operational  
dashboard

Service management Yes Yes

User management Yes Yes

Storage management Yes Yes

Networking Yes Yes

Learning value Excellent for exploring Excellent for system  
administrative concepts monitoring and modern  
Linux operations

The correct choice depends on the environment, distribution, required  
modules, administrative workflow, and organizational standards.

---

# 10. Graphical Administration Does Not Replace the CLI

A professional administrator should be comfortable with both GUI and CLI  
administration.

Consider a service that is not working.

A GUI may show:

```
Service: web.service
Status: Failed
```

The administrator still needs to understand how to investigate it.

For example, on a system using systemd:

```
systemctl status web.service
```

Then inspect logs:

```
journalctl -u web.service
```

Check listening sockets:

```
ss -lntp
```

Check IP configuration:

```
ip addr
```

Check connectivity:

```
ping <destination>
```

The exact commands depend on the operating system and the problem.

### The principle

**GUI = convenience and visibility**

**CLI = precision, automation, scripting, and universal troubleshooting  
capability**

Both are important.

---

# 11. Remote Administration

Servers are frequently administered remotely.

A common Linux administration method is **SSH (Secure Shell)**.

Conceptually:

```
Administrator PC
      |
      | SSH
      v
+----------------+
| Linux Server   |
|                |
| SSH daemon     |
+----------------+
```

A remote administrator can use SSH to:

- Execute commands
    
- Inspect logs
    
- Manage services
    
- Modify configuration
    
- Transfer files using related tools
    
- Automate administrative operations
    

Web-based tools such as Cockpit and Webmin provide another remote  
administration interface through a browser.

### Security principle

Remote administration interfaces are sensitive services.

Administrators should consider:

- Strong authentication
    
- Access restrictions
    
- TLS/HTTPS
    
- Firewall policy
    
- Network segmentation
    
- Account privileges
    
- Logging
    
- Regular updates
    

Never expose an administrative interface to an untrusted network without  
an appropriate security design.

---

# 12. A Practical Administrative Mental Model

When a server has a problem, do not immediately change settings  
randomly.

Use a structured diagnostic model.

## 12.1 Ask five questions

### 1. Is the system reachable?

Check:

- Network connectivity
    
- IP configuration
    
- Routing
    
- Firewall
    

### 2. Is the service running?

Check:

- Service state
    
- Process state
    
- Startup configuration
    

### 3. Is the service listening?

Check:

- Listening address
    
- Port
    
- Protocol
    
- Socket state
    

### 4. Is the application functioning?

Check:

- Application logs
    
- Configuration
    
- Dependencies
    
- Authentication
    
- Database connectivity
    

### 5. What changed?

Investigate:

- Recent updates
    
- Configuration changes
    
- Certificate changes
    
- Network changes
    
- Hardware or storage problems
    

This approach reduces random troubleshooting.

---

# 13. Example: Diagnosing an Unreachable Web Server

Suppose a user reports:

> "The website is down."

Do not immediately reinstall the web server.

Use layers.

### Step 1 --- Check DNS

Does the hostname resolve to the expected address?

```
web.example.local → 192.0.2.10
```

If DNS is wrong, the web server may be completely healthy.

---

### Step 2 --- Check network connectivity

Can the client reach the server?

```
Client → Server
```

If not, investigate routing, VLANs, interfaces, or firewall rules.

---

### Step 3 --- Check the web service

On the server:

```
systemctl status <web-service>
```

If stopped, inspect logs before restarting blindly.

---

### Step 4 --- Check the listening port

For HTTPS:

```
ss -lntp
```

Determine whether something is listening on TCP/443.

---

### Step 5 --- Check the firewall

A service can be running and listening while still being inaccessible  
because a firewall blocks the traffic.

---

### Step 6 --- Check application-level behavior

If the connection reaches the server but the application returns errors,  
investigate:

- Web server configuration
    
- Application logs
    
- Database connectivity
    
- File permissions
    
- Certificates
    
- Application dependencies
    

This example illustrates the relationship between **network  
administration** and **system administration**.

---

# 14. Security Principles for Server Administration

## 14.1 Least privilege

Give users and services only the permissions they require.

---

## 14.2 Minimize attack surface

A server should not run unnecessary services.

For example:

```
Required role:
Web server

Necessary:
HTTP/HTTPS
SSH administration

Unnecessary:
Random services with no business purpose
```

Every unnecessary service can increase complexity and potentially  
increase attack surface.

---

## 14.3 Patch management

Administrators must track:

- Operating system updates
    
- Application updates
    
- Security vulnerabilities
    
- Compatibility requirements
    

Updates should be planned and tested appropriately rather than applied  
blindly in critical environments.

---

## 14.4 Logging

Logs provide evidence about system behavior.

Useful log categories include:

- Authentication
    
- System events
    
- Service failures
    
- Kernel events
    
- Network events
    
- Application events
    

A good administrator learns to ask:

> **What does the system's evidence tell me?**

---

## 14.5 Backups and recovery

A server administration strategy should include:

```
Data
  |
  v
Backup
  |
  v
Protected storage
  |
  v
Recovery test
```

Backup and recovery are related but distinct processes.

**Backup** creates recoverable copies.

**Recovery** restores service or data after an incident.

---

# 15. Core Concepts to Master

Before moving to the next chapter, you should be comfortable with these  
concepts:

### System administration

Management of operating systems, services, users, storage, security,  
monitoring, and maintenance.

### Network administration

Management of connectivity, addressing, routing, DNS, DHCP, firewalls,  
and network services.

### Server OS

An operating system used to provide services and workloads to clients or  
other systems.

### Server role

The logical function performed by a server, such as DNS, web, DHCP,  
database, or file service.

### Webmin

A web-based administration interface providing modular management  
capabilities for Unix-like systems.

### Cockpit

A web-based Linux server management and monitoring interface.

### CLI

Command-line interface; a fundamental administration mechanism for  
precise, scriptable, and remote operations.

### GUI

Graphical user interface; useful for visualization, discoverability, and  
common administrative tasks.

### Monitoring

Continuous observation of system health, availability, performance,  
capacity, and security signals.

### Least privilege

Granting only the permissions necessary for legitimate work.

---

# 16. Practical Lab --- First Server Administration Exercise

## Lab objectives

You will:

1. Identify a server's operating system.
    
2. Identify its hostname and IP address.
    
3. Identify running services.
    
4. Inspect resource usage.
    
5. Examine logs.
    
6. Explore a graphical administration interface.
    
7. Compare GUI actions with CLI concepts.
    

---

## Part A --- Identify the operating system

On a Linux server, inspect the OS information.

```
cat /etc/os-release
```

Record:

- Distribution
    
- Version
    
- Release information
    

---

## Part B --- Identify the hostname

```
hostnamectl
```

Record the hostname.

---

## Part C --- Inspect network configuration

```
ip addr
```

Identify:

- Network interfaces
    
- IPv4 addresses
    
- IPv6 addresses, if present
    
- Interface state
    

Then inspect routes:

```
ip route
```

Identify the default route.

---

## Part D --- Inspect listening services

```
ss -lntup
```

Create a table:

Protocol Local address Port Process/service

---

TCP  
UDP

Do not expose services simply because they are available. Determine  
whether each service is actually required.

---

## Part E --- Inspect system resources

Check disk usage:

```
df -h
```

Check memory:

```
free -h
```

Inspect running processes:

```
ps aux
```

Questions:

1. Which filesystem has the highest utilization?
    
2. How much memory is available?
    
3. Which processes consume significant resources?
    

---

## Part F --- Inspect services

On a system using systemd:

```
systemctl --type=service
```

Choose one service and inspect it:

```
systemctl status <service-name>
```

Answer:

- Is it running?
    
- Is it enabled?
    
- What process is associated with it?
    
- Are there recent errors?
    

---

## Part G --- Inspect logs

Use:

```
journalctl
```

Then narrow the investigation to a service:

```
journalctl -u <service-name>
```

Try to identify:

- Informational events
    
- Warnings
    
- Errors
    

---

## Part H --- Explore a GUI

If your laboratory environment provides **Cockpit** or **Webmin**,  
explore:

- System information
    
- CPU and memory
    
- Storage
    
- Network interfaces
    
- Services
    
- Logs
    
- Users
    

For each operation, ask:

> **What underlying system component is this GUI displaying or  
> modifying?**

This question is essential for developing real administration skills.

---

# 17. Exercises

## Exercise 1 --- Conceptual

Explain the difference between:

1. System administration
    
2. Network administration
    
3. Server OS
    
4. Server role
    
5. Web-based administration interface
    

---

## Exercise 2 --- Server roles

For each server below, identify its likely role:

```
Server A → Provides IP addresses automatically
Server B → Resolves hostnames
Server C → Hosts a website
Server D → Stores shared company documents
Server E → Stores application data
```

---

## Exercise 3 --- Troubleshooting

A server is powered on, but users cannot access its web application.

Create a troubleshooting sequence that checks:

1. DNS
    
2. Network connectivity
    
3. Firewall
    
4. Service status
    
5. Listening port
    
6. Application logs
    

Explain why the order is reasonable.

---

## Exercise 4 --- GUI vs CLI

For each task, explain why a GUI or CLI might be preferable:

- Checking CPU usage
    
- Restarting a service
    
- Searching logs
    
- Repeating a configuration across 50 servers
    
- Exploring unfamiliar system settings
    

There is not always one correct answer. Justify your decision.

---

# 18. Instructor Notes: What I Expect You to Develop

As your mentor, I want you to avoid learning administration as a  
collection of commands to memorize.

Instead, develop the following habits.

## Habit 1 --- Understand before changing

Do not execute commands simply because they appear in a tutorial.

Understand:

- What the command changes
    
- Why the change is required
    
- What could break
    
- How to reverse it
    

---

## Habit 2 --- Observe before troubleshooting

Collect evidence before making changes.

Use:

- Status information
    
- Logs
    
- Network information
    
- Resource statistics
    
- Configuration files
    

---

## Habit 3 --- Think in layers

When a service fails, think:

```
Hardware
   ↓
Operating system
   ↓
Network
   ↓
Service
   ↓
Application
   ↓
Data
```

The exact order of investigation can vary, but layered reasoning  
prevents tunnel vision.

---

## Habit 4 --- Prefer reproducible administration

A professional administrator should be able to explain exactly what was  
done.

Prefer:

```
Documented change
      ↓
Repeatable procedure
      ↓
Verifiable result
```

over:

```
"It worked after I changed some things."
```

---

## Habit 5 --- Treat GUI tools as administration interfaces, not magic

Webmin and Cockpit are valuable tools.

But your goal is not to become someone who can click buttons.

Your goal is to become someone who understands:

> **what the system is doing, why it is doing it, and how to verify that  
> it is doing it correctly.**

---

# 19. Chapter Summary

System and network administration is the disciplined management of  
computing and communication infrastructure.

A server operating system provides the foundation on which server  
workloads and services operate.

The concept of **server roles** allows administrators to describe the  
principal purpose of a server, such as:

- Web
    
- DNS
    
- DHCP
    
- File
    
- Database
    
- Directory/authentication
    

Administration includes much more than installation. It encompasses:

```
Plan
  ↓
Deploy
  ↓
Configure
  ↓
Validate
  ↓
Monitor
  ↓
Maintain
  ↓
Secure
  ↓
Troubleshoot
  ↓
Document
```

**Webmin** and **Cockpit** provide web-based interfaces that simplify  
many administrative tasks. They are useful tools, but they do not  
replace understanding of the operating system, networking, services,  
permissions, logs, and command-line tools.

The most important lesson from this chapter is:

> **Good administration is evidence-driven, structured, secure, and  
> repeatable.**

---

# 20. Knowledge Check

Before considering this chapter complete, answer these questions without  
looking at the lesson.

1. What is system administration?
    
2. What is network administration?
    
3. What is the primary purpose of a server OS?
    
4. What does the term "server role" mean?
    
5. Give five examples of server roles.
    
6. Why might roles be separated across multiple servers?
    
7. What is Webmin?
    
8. What is Cockpit?
    
9. Why should a systems administrator still learn the CLI?
    
10. What is least privilege?
    
11. Why are logs important?
    
12. Why should backups be tested through restoration?
    
13. A web service is running but inaccessible from another machine. Name  
    at least four possible causes.
    
14. Why should an administrator collect evidence before changing  
    configuration?
    
15. Explain the relationship between a GUI administration tool and the  
    underlying operating system.
    

If you can answer these questions clearly and explain your reasoning,  
you have achieved the core objectives of Chapter 1.