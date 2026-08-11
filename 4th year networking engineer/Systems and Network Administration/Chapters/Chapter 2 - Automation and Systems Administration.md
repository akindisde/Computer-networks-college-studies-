# Chapter 2 - Automation and Systems Administration

## Learning Objectives

By the end of this chapter, you should be able to:

- Explain why automation is essential in modern systems  
    administration.
    
- Identify the main objectives, benefits, risks, and challenges of  
    infrastructure automation.
    
- Explain the basic concepts behind **Ansible, Puppet, and Chef**.
    
- Compare Ansible, Puppet, and Chef and identify appropriate use  
    cases.
    
- Automate the management of users, groups, files, and directories.
    
- Automate package installation, removal, updates, and repository  
    management.
    
- Automate service lifecycle management and service configuration.
    
- Understand how automation can be used to manage service logs.
    
- Automate backup and restoration procedures.
    
- Automate operating-system updates and security patches.
    
- Apply the principles of **idempotence, repeatability, declarative  
    configuration, and desired state**.
    
- Design safer automation workflows with validation, logging, testing,  
    rollback, and documentation.
    

---

# 1. Introduction to Automation in Systems Administration

## 1.1 What is administration automation?

**Automation** is the use of software, scripts, configuration-management  
tools, and repeatable procedures to perform administrative tasks with  
limited manual intervention.

Without automation, an administrator may configure servers manually:

```
Server 01 → manual configuration
Server 02 → manual configuration
Server 03 → manual configuration
...
Server 50 → manual configuration
```

This approach can work for a very small environment, but it becomes  
difficult to maintain as the number of systems grows.

With automation:

```
                    +------------------+
                    | Automation       |
                    | Controller       |
                    +--------+---------+
                             |
              +--------------+--------------+
              |              |              |
              v              v              v
          Server 01      Server 02      Server 03
              |              |              |
              +--------------+--------------+
                             |
                       Desired state
```

The administrator defines what should exist, and the automation system  
applies the required configuration.

---

## 1.2 Why automation became necessary

Modern infrastructure can contain:

- Dozens or thousands of servers
    
- Virtual machines
    
- Containers
    
- Cloud instances
    
- Databases
    
- Web servers
    
- Network services
    
- Monitoring systems
    
- Storage systems
    
- Development environments
    
- Production environments
    

Manually configuring each system creates a significant operational  
burden.

For example, imagine 100 Linux servers that must all have:

- The same administrative group
    
- The same monitoring package
    
- A specific configuration file
    
- A particular service enabled
    
- A security update installed
    

Manual administration might require hundreds of individual actions.

Automation turns this into a reproducible procedure.

---

# 2. Objectives and Challenges of Automation

## 2.1 Main objectives

The major objectives of automation are:

### Consistency

The same configuration should be applied consistently across systems.

### Repeatability

A procedure should be executable repeatedly with predictable results.

### Speed

Automated procedures can configure many systems much faster than manual  
administration.

### Reliability

Automation reduces errors caused by repetitive manual work.

### Scalability

The same automation can often manage 5 servers or 500 servers with  
relatively small changes.

### Standardization

Automation allows organizations to define and enforce configuration  
standards.

### Auditability

Automated procedures can provide a record of what was changed and when.

### Reduced operational workload

Administrators can spend less time on repetitive tasks and more time on  
architecture, security, troubleshooting, and optimization.

---

## 2.2 Manual administration versus automation

Consider creating a user account on 50 servers.

### Manual approach

```
Administrator
     |
     +-- Login to Server 01
     +-- Create user
     +-- Configure group
     +-- Set permissions
     +-- Repeat...
     |
     +-- Login to Server 50
```

Problems may include:

- Human error
    
- Forgotten servers
    
- Different configurations
    
- Inconsistent permissions
    
- Lack of documentation
    
- Slow execution
    

### Automated approach

```
Administrator
      |
      v
Automation definition
      |
      +---- Server 01
      +---- Server 02
      +---- Server 03
      ...
      +---- Server 50
```

The desired configuration becomes explicit and reproducible.

---

# 3. Key Concepts of Infrastructure Automation

## 3.1 Desired state

The **desired state** describes how a system should look after  
automation has been applied.

For example:

```
Desired state:

User: alice
Group: administrators
Directory: /srv/project
Package: nginx
Service: nginx → running
Firewall: HTTPS → allowed
```

The automation tool compares the current state with the desired state  
and performs the necessary operations.

---

## 3.2 Idempotence

**Idempotence** is one of the most important concepts in configuration  
management.

An operation is idempotent when applying it multiple times produces the  
same intended final state.

For example:

```
Ensure user "alice" exists.
```

First execution:

```
User does not exist
       ↓
Create alice
```

Second execution:

```
User already exists
       ↓
No change required
```

Third execution:

```
User already exists
       ↓
No change required
```

The desired state remains stable.

This is preferable to a script that blindly executes:

```
create user
```

every time.

---

## 3.3 Declarative versus imperative administration

### Imperative approach

You specify **how** to perform each action.

Example conceptually:

```
1. Check whether the directory exists.
2. If it does not exist, create it.
3. Set permissions.
4. Set ownership.
5. Verify the result.
```

### Declarative approach

You specify **what the final state should be**:

```
Directory /srv/project:
    exists
    owner = project
    group = project
    permissions = 0750
```

The automation engine determines the actions necessary to reach that  
state.

Many configuration-management systems emphasize declarative  
configuration.

---

# 4. Introduction to Ansible, Puppet, and Chef

Three important automation/configuration-management technologies are:

- **Ansible**
    
- **Puppet**
    
- **Chef**
    

They solve overlapping problems, but their architectures and workflows  
differ.

---

# 5. Ansible

## 5.1 What is Ansible?

**Ansible** is an automation and configuration-management platform  
commonly used to automate servers, applications, network devices, and  
other infrastructure.

A major characteristic of Ansible is its generally **agentless** model  
for Linux/Unix hosts: it commonly connects remotely using SSH rather  
than requiring a persistent Ansible agent on every managed host.

For Windows systems, Ansible can use Windows-specific remote-management  
mechanisms.

A simplified architecture is:

```
                 Ansible Control Node
                         |
              +----------+----------+
              |          |          |
             SSH        SSH        SSH
              |          |          |
              v          v          v
           Server A   Server B   Server C
```

---

## 5.2 Ansible inventory

An **inventory** identifies the systems Ansible manages.

Conceptually:

```
[webservers]
web01
web02
web03

[databases]
db01
db02
```

Inventory groups allow administrators to target classes of systems.

For example:

```
webservers
databases
production
development
```

---

## 5.3 Ansible playbooks

Ansible commonly uses **YAML** playbooks to describe automation.

Conceptual example:

```
- name: Install web server
  hosts: webservers
  become: true

  tasks:
    - name: Install nginx
      package:
        name: nginx
        state: present

    - name: Ensure nginx is running
      service:
        name: nginx
        state: started
        enabled: true
```

The important idea is not memorizing syntax.

The playbook expresses a desired state:

```
Package nginx → present
Service nginx → running
Service nginx → enabled
```

---

## 5.4 Ansible modules

Ansible performs many tasks through **modules**.

Examples include modules for:

- Packages
    
- Users
    
- Groups
    
- Files
    
- Services
    
- Templates
    
- Copying files
    
- Commands
    
- Network configuration
    

Modules provide structured operations instead of forcing administrators  
to write every operation as a shell command.

---

# 6. Puppet

## 6.1 What is Puppet?

**Puppet** is a configuration-management platform that uses a  
declarative model to describe the desired state of infrastructure.

Puppet environments commonly use a **Puppet agent** on managed systems  
and a **Puppet server** or related control infrastructure.

Conceptually:

```
              Puppet Server
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Agent A   Agent B   Agent C
```

---

## 6.2 Puppet manifests

Puppet configurations are commonly expressed using **Puppet manifests**.

A conceptual resource might describe:

```
Package nginx:
    ensure = installed
```

The important principle is:

> Describe the desired configuration instead of manually executing every  
> command required to reach it.

---

## 6.3 Puppet strengths

Puppet is well suited to:

- Continuous configuration management
    
- Enforcing system standards
    
- Large infrastructures
    
- Long-lived managed environments
    
- Declarative infrastructure management
    

---

# 7. Chef

## 7.1 What is Chef?

**Chef** is an automation and configuration-management platform based  
heavily on defining infrastructure as code.

Chef commonly uses:

- A Chef server
    
- Chef clients
    
- Cookbooks
    
- Recipes
    
- Resources
    

Conceptually:

```
                Chef Server
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Client A   Client B   Client C
```

---

## 7.2 Chef cookbooks and recipes

A **cookbook** packages automation code and supporting files.

A **recipe** describes configuration tasks.

Chef uses a resource-based model to describe desired configuration.

For example, conceptually:

```
Package "nginx":
    action = install

Service "nginx":
    action = start
```

---

# 8. Ansible vs Puppet vs Chef

The tools overlap considerably, so selection should be based on  
architecture, team skills, ecosystem, operational requirements, and  
existing infrastructure.

---

Characteristic Ansible Puppet Chef

---

Typical model Agentless remote Agent-based Agent-based  
automation configuration configuration  
management management

Common transport SSH for Puppet agent Chef client  
Linux/Unix communication communication

Configuration YAML playbooks Declarative Recipes/cookbooks  
style manifests

Learning curve Generally Moderate Moderate to  
approachable advanced

Strength Automation and Continuous Infrastructure as  
orchestration configuration code and complex  
enforcement automation

Central server Not inherently Commonly used Commonly used  
requirement required for  
basic operation

### Important lesson

Do not select a tool simply because it is popular.

Ask:

1. What systems must be managed?
    
2. Is agentless operation important?
    
3. What skills does the team already have?
    
4. What existing automation exists?
    
5. How large is the infrastructure?
    
6. How important is continuous configuration enforcement?
    
7. What integrations are required?
    
8. How will the automation be tested and maintained?
    

---

# 9. Automating Users and Groups

User management is a common automation task.

A standardized server may require:

```
Users:
    alice
    bob
    charlie

Groups:
    developers
    operators
    administrators
```

Automation can ensure that:

- Required users exist
    
- Unwanted users are removed or disabled
    
- Groups exist
    
- Users belong to the correct groups
    
- Shells are configured correctly
    
- Home directories exist
    
- Permissions are correct
    

---

## 9.1 Why automate user management?

Manual account management can produce inconsistencies.

For example:

```
Server 01 → alice belongs to developers
Server 02 → alice belongs to developers
Server 03 → alice belongs to developers + operators
Server 04 → alice does not exist
```

Automation can enforce a standard.

---

## 9.2 Example Ansible concept

A conceptual task:

```
- name: Ensure developer group exists
  ansible.builtin.group:
    name: developers
    state: present

- name: Ensure Alice exists
  ansible.builtin.user:
    name: alice
    groups: developers
    append: true
    state: present
```

The key point is **state**, not the specific syntax.

---

# 10. Automating Files and Directories

Automation can manage:

- Directories
    
- Files
    
- Ownership
    
- Permissions
    
- Symbolic links
    
- Configuration files
    
- Templates
    

For example, an application might require:

```
/opt/application/
├── bin/
├── config/
├── logs/
└── data/
```

Automation can ensure this structure exists.

---

## 10.1 File permissions

Linux permissions are fundamental.

For example:

```
-rwxr-x---
```

These permissions correspond to:

```
Owner  → rwx
Group  → r-x
Others → ---
```

Automation should manage permissions deliberately.

Incorrect permissions can cause either:

- Application failures
    
- Security vulnerabilities
    

---

# 11. Automating Package Management

Package management is one of the most useful areas for automation.

A package manager can install, remove, update, and track software.

Examples include:

Distribution family Package manager

---

Debian/Ubuntu APT  
RHEL/Fedora family DNF  
Arch Linux Pacman

Automation tools abstract many package-management operations.

---

# 12. Automated Package Installation

Suppose every web server must have Nginx installed.

Instead of manually running commands on each machine:

```
Server 01 → install nginx
Server 02 → install nginx
Server 03 → install nginx
...
```

Automation expresses:

```
For all web servers:
    nginx = present
```

Example concept:

```
- name: Ensure nginx is installed
  ansible.builtin.package:
    name: nginx
    state: present
```

---

# 13. Automated Package Removal

Automation can also ensure that unnecessary software is absent.

Conceptually:

```
- name: Ensure unwanted package is absent
  ansible.builtin.package:
    name: example-package
    state: absent
```

This is useful for:

- Reducing attack surface
    
- Removing obsolete software
    
- Standardizing server builds
    
- Cleaning development packages from production environments
    

---

# 14. Automated Package Updates

Automation can manage updates across many servers.

Possible strategies include:

### Full update

Update all available packages.

### Security-focused update

Apply security-related updates according to the organization's patching  
strategy.

### Controlled update

Update a defined set of packages.

Production environments should not treat updates as a blind operation.

Consider:

- Compatibility
    
- Dependencies
    
- Maintenance windows
    
- Reboots
    
- Application availability
    
- Rollback/recovery procedures
    

---

# 15. Package Repository Management

A package repository is a source from which packages are obtained.

Automation can configure:

- Repository URLs
    
- Repository files
    
- Repository enablement
    
- Repository priorities
    
- GPG/signing configuration
    
- Internal repositories
    

Example concept:

```
Server
  |
  v
Repository configuration
  |
  v
Approved package source
  |
  v
Package manager
  |
  v
Installed software
```

Centralizing repository configuration helps organizations control  
software sources.

---

# 16. Automating Service Management

A **service** is a long-running process that provides functionality to  
the system or network.

Examples:

- SSH
    
- Web server
    
- DNS server
    
- Database
    
- Monitoring agent
    
- Logging service
    

Automation can control the service lifecycle.

---

## 16.1 Starting services

Desired state:

```
nginx → running
```

Automation verifies whether the service is running and starts it if  
necessary.

---

## 16.2 Stopping services

Desired state:

```
unwanted-service → stopped
```

Automation ensures the service is not running.

---

## 16.3 Enabling services at boot

There is an important distinction:

```
Running now
```

versus:

```
Enabled at boot
```

A service may be running now but not configured to start after reboot.

Automation should explicitly define the required behavior.

Conceptually:

```
- name: Ensure nginx is running and enabled
  ansible.builtin.service:
    name: nginx
    state: started
    enabled: true
```

---

# 17. Automating Service Configuration

A service is rarely useful without correct configuration.

For example, a web server may require:

```
/etc/nginx/
├── nginx.conf
└── conf.d/
```

Automation can deploy configuration files using:

- Static files
    
- Templates
    
- Variables
    
- Environment-specific configuration
    

---

## 17.1 Templates

Templates allow one configuration structure to be adapted to different  
servers.

For example:

```
Server A:
    hostname = web01
    port = 443

Server B:
    hostname = web02
    port = 443
```

The same template can generate both configurations using variables.

This reduces duplication.

---

## 17.2 Configuration change workflow

A safe configuration workflow is:

```
Generate configuration
        ↓
Validate syntax
        ↓
Deploy configuration
        ↓
Restart/reload service
        ↓
Check service status
        ↓
Check logs
        ↓
Test functionality
```

Do not automatically restart a critical service after every  
configuration change without validation.

---

# 18. Automating Service Logs

Logs are essential for troubleshooting and auditing.

Automation can help configure:

- Log locations
    
- Log rotation
    
- Retention periods
    
- Log collection
    
- Centralized logging
    
- Permissions
    
- Monitoring alerts
    

---

## 18.1 Log rotation

A server can generate large amounts of log data.

Without rotation:

```
application.log
     ↓
100 MB
     ↓
1 GB
     ↓
10 GB
     ↓
Disk full
```

Automation can configure log rotation and retention.

A common Linux mechanism is **logrotate**, although logging  
architectures vary.

---

## 18.2 Centralized logs

In larger environments, logs should often be collected centrally.

```
Server 01 ─┐
Server 02 ─┼──> Central log system
Server 03 ─┘
```

Advantages include:

- Easier investigation
    
- Central retention
    
- Correlation between systems
    
- Security monitoring
    
- Reduced dependence on local disks
    

Automation can configure the required logging agents and configuration  
consistently.

---

# 19. Automating Backups

Backup automation is critical because manual backups are easy to forget.

A backup policy should define:

- What is backed up?
    
- How frequently?
    
- Where is it stored?
    
- How long is it retained?
    
- How is it protected?
    
- How is restoration tested?
    

---

## 19.1 Data backup

Data may include:

- User files
    
- Databases
    
- Application data
    
- Configuration data
    
- Critical documents
    

A conceptual workflow:

```
Production data
      |
      v
Backup job
      |
      v
Backup storage
      |
      v
Verification
      |
      v
Retention
```

---

# 20. Configuration Backups

Configuration backups are often overlooked.

Examples include:

```
/etc/ssh/
 /etc/nginx/
 /etc/systemd/
 /etc/network/
```

The exact paths depend on the operating system and service.

Configuration backup is useful because rebuilding a server is not just  
about restoring user data.

You may also need:

- Service configuration
    
- User/group information
    
- Scheduled tasks
    
- Firewall rules
    
- Repository definitions
    
- Application configuration
    

---

# 21. Backup Verification

A successful backup job does not automatically mean a successful  
recovery capability.

Automation should verify:

1. The backup job completed.
    
2. The expected files exist.
    
3. The backup is readable.
    
4. The backup has not been corrupted.
    
5. The backup meets retention requirements.
    
6. Restoration procedures work.
    

---

# 22. Automating Restoration

Restoration should be treated as an operational procedure.

A simplified workflow:

```
Incident
   ↓
Identify required recovery point
   ↓
Retrieve backup
   ↓
Validate backup
   ↓
Restore data/configuration
   ↓
Validate services
   ↓
Validate application
   ↓
Return to operation
```

Automation can make this procedure repeatable.

---

## 22.1 Backup versus recovery

These concepts must not be confused.

### Backup

Creating a recoverable copy.

### Recovery

Using the backup to restore data or service functionality.

A mature infrastructure has both.

---

# 23. Automating Operating-System Updates and Patches

## 23.1 What is patch management?

**Patch management** is the controlled process of identifying, testing,  
deploying, and verifying software updates.

Updates may address:

- Security vulnerabilities
    
- Bugs
    
- Performance issues
    
- Compatibility
    
- New functionality
    

---

## 23.2 Why automation matters

Consider 200 servers.

Manual patching:

```
200 servers × manual intervention
```

Automation can instead apply a controlled policy:

```
Patch policy
     |
     +-- Development
     +-- Testing
     +-- Staging
     +-- Production
```

This enables controlled rollout.

---

# 24. Patch Management Strategy

A professional patching process might look like:

```
Identify updates
       ↓
Assess risk
       ↓
Test
       ↓
Backup / verify recovery
       ↓
Patch pilot systems
       ↓
Validate
       ↓
Patch larger group
       ↓
Monitor
       ↓
Document
```

This is safer than:

```
Update every production server immediately
```

---

# 25. Rolling Updates

In environments with multiple servers, updates can be applied  
progressively.

Suppose there are four web servers:

```
web01
web02
web03
web04
```

Instead of stopping all four:

```
Take web01 out of service
        ↓
Patch web01
        ↓
Test web01
        ↓
Return web01
        ↓
Patch web02
        ↓
...
```

This can preserve service availability if the architecture supports  
redundancy.

This concept is often called a **rolling update** or **rolling  
deployment**.

---

# 26. Reboots and Patch Automation

Some updates require a reboot.

Automation should distinguish between:

```
Patch completed
```

and:

```
Patch completed + reboot required
```

A safe automation workflow may:

1. Detect whether a reboot is required.
    
2. Verify maintenance policy.
    
3. Remove the server from service if appropriate.
    
4. Reboot.
    
5. Wait for the system to return.
    
6. Verify connectivity.
    
7. Verify required services.
    
8. Return the system to service.
    

---

# 27. Example: Complete Server Baseline

Imagine a new Ubuntu server that will become a web server.

The desired baseline is:

```
Hostname:
    web01

Users:
    admin
    deploy

Groups:
    webadmins

Packages:
    nginx
    curl
    monitoring-agent

Directories:
    /srv/web
    /var/log/application

Services:
    nginx → running
    nginx → enabled

Firewall:
    HTTPS → allowed

Updates:
    Security updates → applied

Monitoring:
    Agent → installed and active

Backup:
    Configuration → backed up
```

Without automation, an administrator performs each task manually.

With automation, this baseline becomes code.

---

# 28. Example Ansible Structure

A maintainable Ansible project might be organized like:

```
ansible-project/
├── inventory/
│   ├── production
│   └── development
├── group_vars/
│   ├── webservers.yml
│   └── databases.yml
├── playbooks/
│   ├── baseline.yml
│   ├── webservers.yml
│   └── patching.yml
└── roles/
    ├── common/
    ├── nginx/
    ├── monitoring/
    └── backup/
```

This organization separates:

- Inventory
    
- Variables
    
- Playbooks
    
- Reusable roles
    

---

# 29. Automation Safety

Automation is powerful.

That means automation can also make mistakes at scale.

A manual mistake might affect:

```
1 server
```

An automated mistake might affect:

```
500 servers
```

Therefore:

> **The larger the automation scope, the more important testing and  
> controls become.**

---

## 29.1 Test before production

A typical progression is:

```
Developer workstation
        ↓
Test environment
        ↓
Staging
        ↓
Production
```

Do not develop destructive automation directly against production  
whenever avoidable.

---

## 29.2 Limit scope

Start with a small target.

For example:

```
1 server
   ↓
5 servers
   ↓
10 servers
   ↓
Production fleet
```

This is especially important for:

- Package updates
    
- Configuration changes
    
- Firewall changes
    
- User removal
    
- Service restarts
    
- Database changes
    

---

## 29.3 Back up before risky operations

Before modifying critical configuration or data:

```
Backup
   ↓
Change
   ↓
Validate
   ↓
Rollback if necessary
```

---

# 30. Automation and Security

Automation itself must be secured.

Protect:

- Automation credentials
    
- SSH keys
    
- API tokens
    
- Passwords
    
- Configuration repositories
    
- Automation servers
    
- Inventory information
    

Do not store secrets in plain text in source repositories.

Use appropriate secret-management mechanisms.

---

# 31. Version Control and Infrastructure as Code

Automation definitions should generally be treated as code.

Use version control such as Git to track:

- Playbooks
    
- Roles
    
- Manifests
    
- Recipes
    
- Templates
    
- Configuration files
    

A change can then be reviewed:

```
Before
   ↓
Change proposed
   ↓
Review
   ↓
Test
   ↓
Merge
   ↓
Deploy
```

This introduces software-engineering practices into systems  
administration.

---

# 32. Configuration Drift

**Configuration drift** occurs when systems that should be identical  
gradually become different.

Example:

```
Expected:

web01 = web02 = web03

Actual:

web01 → nginx 1.x
web02 → nginx 1.x
web03 → manually modified configuration
```

Drift can occur because administrators make manual changes over time.

Automation can reduce drift by continuously or repeatedly enforcing the  
desired state.

---

# 33. Automation and Documentation

Automation is itself a form of documentation.

Compare:

```
Document:
"Install nginx and configure it."
```

with:

```
Automation:
- Install nginx
- Deploy configuration
- Validate configuration
- Enable service
- Start service
- Verify service
```

The second form is both executable and explicit.

However, automation does not eliminate the need for documentation.

You should still document:

- Purpose
    
- Architecture
    
- Dependencies
    
- Variables
    
- Secrets management
    
- Deployment process
    
- Recovery procedure
    
- Known limitations
    

---

# 34. Common Automation Mistakes

## Mistake 1 --- Automating without understanding

Automation does not compensate for weak system knowledge.

---

## Mistake 2 --- Using shell commands for everything

A configuration-management module is often safer and more idempotent  
than arbitrary shell commands.

---

## Mistake 3 --- No testing

A broken automation task can affect an entire infrastructure.

---

## Mistake 4 --- No rollback plan

Critical changes should have a recovery strategy.

---

## Mistake 5 --- Hardcoding secrets

Passwords and API tokens should not be embedded carelessly in automation  
code.

---

## Mistake 6 --- Excessive privileges

Automation accounts should have only the privileges required for their  
tasks.

---

## Mistake 7 --- No change control

Production automation should be reviewed and tracked.

---

## Mistake 8 --- Ignoring logs

Automation failures should produce enough information to determine what  
happened.

---

# 35. Practical Lab --- Ansible Fundamentals

## Lab Objectives

You will build an Ansible automation environment and automate:

1. User creation
    
2. Group management
    
3. Directory creation
    
4. Package installation
    
5. Service management
    
6. Configuration deployment
    
7. Basic update management
    

---

## Part A --- Lab Architecture

Use:

```
                    Control Node
                  Ansible installed
                         |
                    SSH / network
                         |
          +--------------+--------------+
          |                             |
          v                             v
      Managed01                     Managed02
       Linux VM                      Linux VM
```

You can use virtual machines such as:

- VirtualBox
    
- VMware
    
- KVM/libvirt
    
- A cloud environment
    

---

## Part B --- Verify connectivity

From the control node, test SSH connectivity to the managed systems.

Then verify that Ansible can communicate with them.

Conceptually:

```
ansible all -i inventory -m ping
```

Expected result:

```
Managed01 → SUCCESS
Managed02 → SUCCESS
```

The Ansible `ping` module tests Ansible connectivity and execution; it  
is not the same thing as the network `ping` utility.

---

# 36. Lab --- Manage Users and Groups

Create a playbook that ensures:

```
Group:
    sysadmins

User:
    student

Membership:
    student → sysadmins
```

Then execute it.

Run it again.

### Question

Why is the second execution important?

You are testing **idempotence**.

---

# 37. Lab --- Manage Directories

Ensure that:

```
/opt/training
```

exists with appropriate ownership and permissions.

Run the automation twice.

Verify:

- Directory exists
    
- Ownership is correct
    
- Permissions are correct
    
- Second execution produces no unnecessary changes
    

---

# 38. Lab --- Manage Packages

Create automation that ensures a package is installed.

For example:

```
curl → installed
```

Then change the desired state:

```
curl → absent
```

Observe the difference.

This teaches the relationship between:

```
Desired state
      ↓
Automation engine
      ↓
Current state
      ↓
Required change
```

---

# 39. Lab --- Manage Services

Automate a service so that it is:

```
Installed
Running
Enabled at boot
```

Then verify:

```
systemctl status <service>
```

Also check whether the service is listening where expected.

---

# 40. Lab --- Configuration Management

Create a configuration file using an Ansible template.

Example conceptual file:

```
Application name: {{ application_name }}
Environment: {{ environment }}
Server: {{ inventory_hostname }}
```

Use variables to generate different configurations on different servers.

For example:

```
Managed01:
    Environment = development

Managed02:
    Environment = testing
```

This introduces the principle of **parameterized configuration**.

---

# 41. Lab --- Updates

Create a controlled patching playbook.

Your workflow should include:

```
Identify targets
      ↓
Check current state
      ↓
Apply updates
      ↓
Check whether reboot is required
      ↓
Reboot if policy permits
      ↓
Wait for reconnection
      ↓
Verify services
```

Do not perform this exercise on a production system.

---

# 42. Lab --- Backup and Restore

Create a simple configuration backup procedure.

Back up selected configuration files into a dedicated backup directory.

For example:

```
/etc/ssh/
/etc/hosts
/etc/hostname
```

Then:

1. Verify the backup exists.
    
2. Modify a test configuration.
    
3. Restore the backup.
    
4. Validate the restored configuration.
    
5. Document the recovery procedure.
    

The purpose is not merely to create a backup.

The objective is to demonstrate that **restoration works**.

---

# 43. Troubleshooting Automation

When automation fails, do not immediately rewrite everything.

Follow a structured process.

## Step 1 --- Read the error

Identify:

- Task
    
- Host
    
- Module
    
- Error message
    

## Step 2 --- Determine the failure layer

Possible causes include:

```
Network
SSH
Authentication
Privileges
Package repository
Operating system
Module
Configuration
Application
```

## Step 3 --- Reproduce manually when useful

A manual test can help determine whether the issue is:

- The automation
    
- The underlying system
    
- The network
    
- The service
    

## Step 4 --- Fix the root cause

Avoid hiding failures with arbitrary commands.

## Step 5 --- Re-run safely

Test against a limited target first.

---

# 44. A Professional Automation Workflow

A mature workflow can be summarized as:

```
Requirement
    ↓
Design desired state
    ↓
Write automation
    ↓
Version control
    ↓
Review
    ↓
Test
    ↓
Validate
    ↓
Deploy gradually
    ↓
Monitor
    ↓
Document
```

This process is applicable to:

- User management
    
- Packages
    
- Services
    
- Configuration
    
- Backups
    
- Updates
    
- Security controls
    

---

# 45. Chapter Summary

Automation transforms systems administration from a collection of manual  
operations into a **repeatable, scalable, and controlled engineering  
process**.

The major objectives of automation are:

- Consistency
    
- Repeatability
    
- Speed
    
- Reliability
    
- Scalability
    
- Standardization
    
- Auditability
    

Three major configuration-management tools introduced in this chapter  
are:

- **Ansible**
    
- **Puppet**
    
- **Chef**
    

The central concepts are:

```
Desired State
     +
Idempotence
     +
Declarative Configuration
     +
Version Control
     +
Testing
     +
Monitoring
     =
Reliable Automation
```

Automation can be applied to:

- Users
    
- Groups
    
- Files
    
- Directories
    
- Packages
    
- Repositories
    
- Services
    
- Service configurations
    
- Logs
    
- Backups
    
- Restorations
    
- Operating-system updates
    
- Security patches
    

The most important principle is:

> **Automation should make administration more predictable, not merely  
> faster.**

A fast process that consistently makes the wrong change is more  
dangerous than a slow manual process.

---

# 46. Knowledge Check

Answer these questions without consulting the chapter.

1. What is systems-administration automation?
    
2. Why does automation become more important as infrastructure grows?
    
3. Define **desired state**.
    
4. Explain **idempotence** with an example.
    
5. What is the difference between imperative and declarative  
    configuration?
    
6. What is Ansible?
    
7. What is an Ansible inventory?
    
8. What is an Ansible playbook?
    
9. What is a Puppet manifest?
    
10. What are Chef cookbooks and recipes?
    
11. Give two important differences between Ansible and Puppet.
    
12. Why should users and groups be automated?
    
13. Why should package installation be automated?
    
14. What is the difference between a service being **running** and  
    **enabled**?
    
15. Why is service configuration validation important?
    
16. Why is log rotation necessary?
    
17. What is the difference between a data backup and a configuration  
    backup?
    
18. Why must backups be tested through restoration?
    
19. What is patch management?
    
20. Why are rolling updates useful?
    
21. What is configuration drift?
    
22. Why should automation code be stored in version control?
    
23. Why can automation be dangerous when incorrectly designed?
    
24. What should happen before deploying a major automation change to  
    production?
    
25. Design a high-level automation workflow for patching 100 web servers  
    safely.
    

---

# 47. Mentor Challenge

Design an automated baseline for a new Linux web server.

Your desired state should specify:

```
Identity:
    Hostname
    IP/network configuration

Users:
    Required administrative users

Groups:
    Required groups

Packages:
    Required packages
    Unwanted packages

Files/directories:
    Required paths
    Ownership
    Permissions

Services:
    Required services
    Running state
    Boot state

Configuration:
    Web server configuration
    Monitoring configuration

Security:
    Firewall policy
    Required updates

Backup:
    Configuration backup
    Application/data backup

Monitoring:
    Logs
    Service health
    Resource usage
```

Then answer:

1. Which parts should be automated?
    
2. Which tasks should be idempotent?
    
3. Which tasks require validation?
    
4. Which tasks could cause downtime?
    
5. What should be backed up before making changes?
    
6. How would you test the automation?
    
7. How would you roll it back?
    
8. How would you deploy it safely to 100 servers?
    

If you can design this baseline coherently, you are beginning to think  
like an infrastructure engineer rather than simply an administrator  
executing commands.