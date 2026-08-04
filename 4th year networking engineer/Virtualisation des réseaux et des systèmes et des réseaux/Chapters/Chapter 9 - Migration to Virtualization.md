# Chapter 9 — Migration to Virtualization

# Learning Objectives

By the end of this chapter, you should be able to explain what migration to virtualization means, determine whether an organization is ready for virtualization, assess existing physical workloads, identify dependencies and risks, design a realistic migration plan, select an appropriate migration strategy, execute a migration safely, validate the result, and prepare a rollback or recovery procedure.

The most important idea in this chapter is that **virtualization migration is not simply a technical conversion from physical servers to virtual machines**.

A physical server can be converted into a VM, but that does not automatically mean the migration was successful.

A successful migration must preserve or improve:

- Application availability
    
- Data integrity
    
- Network connectivity
    
- Performance
    
- Security
    
- Manageability
    
- Backup and recovery
    
- Operational reliability
    
- Business continuity
    

A migration is therefore both a **technical project** and a **business continuity project**.

---

# 1. Introduction to Migration to Virtualization

Migration to virtualization is the process of moving workloads, applications, services, and infrastructure from physical or non-virtualized systems into a virtualized environment.

A simple example is a company running three physical servers:

```
Physical Server 1
Web Application

Physical Server 2
Database

Physical Server 3
File Server
```

The company could introduce a virtualization host and move those workloads into virtual machines:

```
              Physical Host
                   |
               Hypervisor
        +----------+----------+
        |          |          |
       VM1        VM2        VM3
       Web      Database    Files
```

At first glance, the project seems straightforward.

However, the virtualization engineer must answer many questions.

How much CPU does each workload actually consume?

How much RAM does it really need?

How much storage is required?

Which applications communicate with one another?

Which workloads are business-critical?

Which systems can tolerate downtime?

Which systems must remain available continuously?

Are there old operating systems that may not be supported by the virtualization platform?

Are there applications that depend on physical hardware?

Can the existing backup system protect virtual machines?

What happens if the migration fails?

These questions show why migration must be planned systematically.

---

# 2. Why Organizations Migrate to Virtualization

Organizations usually adopt virtualization because they want to improve infrastructure efficiency, flexibility, availability, or management.

One common motivation is server consolidation.

Imagine an organization with ten physical servers.

Each server may have 16 CPU cores, but the average CPU utilization might be only 10–20%.

The organization is therefore paying for hardware capacity that is rarely used.

Virtualization can consolidate workloads:

```
10 Physical Servers
       |
       v
3 Virtualization Hosts
       |
       +-- VM1
       +-- VM2
       +-- VM3
       +-- ...
       +-- VM20
```

The physical infrastructure becomes more densely utilized.

However, consolidation also creates a new dependency: multiple workloads may now depend on the same physical host.

This is why migration planning must consider not only efficiency but also **failure domains**.

---

# 3. Common Objectives of Virtualization Migration

Before beginning a migration, the organization should define why it is migrating.

Possible objectives include:

- Reducing physical server count
    
- Reducing hardware costs
    
- Improving resource utilization
    
- Simplifying administration
    
- Improving disaster recovery
    
- Increasing workload mobility
    
- Standardizing server deployments
    
- Improving backup and restoration
    
- Preparing for high availability
    
- Supporting cloud or hybrid infrastructure
    
- Reducing data-center space
    
- Reducing power consumption
    

The objective matters because it determines the architecture.

For example, if the main goal is simply server consolidation, the organization may prioritize resource efficiency.

If the goal is disaster recovery, it must additionally prioritize replication, backup, recovery time, and recovery point objectives.

---

# 4. Migration Is Not the Same as Virtualization

It is important to distinguish three different concepts.

**Virtualization** is the technology that allows workloads to run in virtual environments.

**Migration** is the process of moving workloads from one environment to another.

**Modernization** is the process of changing or improving the workload itself.

For example:

```
Physical Server
      |
      | P2V
      v
Virtual Machine
```

This is virtualization migration.

But:

```
Physical Application
      |
Rewrite application
      |
Containerized application
      |
Cloud platform
```

is modernization.

Organizations sometimes combine these approaches, but they should not be confused.

---

# 5. Physical-to-Virtual Migration

A common migration strategy is called **P2V**, meaning **Physical-to-Virtual**.

The idea is to convert a physical server into a virtual machine.

Conceptually:

```
Physical Server
     |
     | P2V
     v
Virtual Machine
```

The operating system and applications are preserved while the underlying hardware model changes.

The process may involve:

1. Inventorying the physical server.
    
2. Measuring resource utilization.
    
3. Preparing the target virtualization environment.
    
4. Converting or copying the disks.
    
5. Adjusting drivers and virtual hardware.
    
6. Configuring networking.
    
7. Testing the VM.
    
8. Performing the final cutover.
    
9. Validating the application.
    
10. Retiring the physical server only after successful verification.
    

P2V can reduce migration effort because the application does not necessarily need to be reinstalled from scratch.

However, it can also carry old problems into the virtual environment.

---

# 6. Virtual-to-Virtual Migration

Not all migrations start from physical servers.

An organization may already use virtualization but want to move workloads between platforms.

For example:

```
Hypervisor A
     |
     | V2V
     v
Hypervisor B
```

This is commonly called **V2V**, or **Virtual-to-Virtual migration**.

Examples include moving a workload between different virtualization platforms or between different storage architectures.

V2V can be complicated by differences in:

- Virtual disk formats
    
- Virtual hardware
    
- Network configuration
    
- CPU models
    
- Firmware configuration
    
- Guest drivers
    
- Snapshot systems
    
- Storage controllers
    

The application may be unchanged, but the virtual hardware underneath it can change significantly.

---

# 7. Physical-to-Virtual Versus Rebuild

There are two broad ways to migrate a server.

The first is to convert the existing physical system.

The second is to create a new VM and migrate the application and data into it.

For example:

```
Option A: P2V

Physical Server
       |
       v
Virtual Machine
```

or:

```
Option B: Rebuild

Physical Server
       |
       +---- Application/Data
                    |
                    v
               New VM
```

P2V may be faster.

Rebuilding can produce a cleaner and more maintainable system.

The correct choice depends on the application, operating system, downtime requirements, licensing, compatibility, and migration complexity.

---

# 8. Why Migration Projects Fail

Migration projects can fail for technical and organizational reasons.

Common examples include:

- Incomplete inventory
    
- Unknown application dependencies
    
- Incorrect resource sizing
    
- Insufficient storage
    
- Incorrect network configuration
    
- Unsupported operating systems
    
- Application licensing problems
    
- Inadequate testing
    
- Unexpected downtime
    
- Poor backup strategy
    
- No rollback plan
    
- Insufficient staff training
    
- Inadequate monitoring
    
- Underestimating data transfer time
    

A common mistake is to treat migration as a file-copying exercise.

The difficult part is often not copying the VM.

The difficult part is ensuring that **everything depending on the old environment continues working afterward**.

---

# 9. The Migration Lifecycle

A professional migration can be represented as:

```
Assessment
    |
    v
Requirements Analysis
    |
    v
Migration Strategy
    |
    v
Design
    |
    v
Planning
    |
    v
Pilot
    |
    v
Migration Preparation
    |
    v
Execution
    |
    v
Validation
    |
    v
Monitoring
    |
    v
Optimization
    |
    v
Decommissioning
```

Each stage has a purpose.

Skipping stages can create problems later.

---

# 10. Phase 1 — Assess the Existing Environment

Before changing anything, understand what already exists.

This is called **discovery** or **assessment**.

The assessment should identify:

- Physical servers
    
- Operating systems
    
- Applications
    
- Databases
    
- Storage
    
- Network interfaces
    
- IP addresses
    
- DNS records
    
- Dependencies
    
- Resource consumption
    
- Backup systems
    
- Security controls
    
- Monitoring
    
- Hardware dependencies
    
- Business criticality
    

The result should be an accurate picture of the current infrastructure.

---

# 11. Infrastructure Inventory

An inventory might contain:

|Server|OS|Role|CPU|RAM|Storage|Criticality|
|---|---|---|---|---|---|---|
|Server01|Linux|Web|8 cores|16 GB|200 GB|Medium|
|Server02|Linux|Database|16 cores|64 GB|1 TB|Critical|
|Server03|Windows|File Server|8 cores|32 GB|2 TB|High|
|Server04|Linux|Monitoring|4 cores|8 GB|100 GB|Medium|

The table itself is not enough.

You also need to know how these systems interact.

For example:

```
Users
  |
Web Server
  |
Application Server
  |
Database
  |
Storage
```

The dependency relationships may be more important than the server specifications.

---

# 12. Application Dependency Mapping

Application dependency mapping identifies which systems communicate with one another.

For example:

```
Web VM
 |
 | TCP 443
 v
Application VM
 |
 | TCP 5432
 v
Database VM
 |
 | Storage
 v
Database Storage
```

If the application VM is migrated before the database, the application may stop working.

Therefore, migration order matters.

A migration plan should consider dependencies rather than simply migrating servers alphabetically.

---

# 13. Discovering Dependencies

Dependencies can be identified using:

- Documentation
    
- Interviews with administrators
    
- Application owners
    
- Network flow data
    
- DNS records
    
- Firewall logs
    
- Monitoring
    
- Process inspection
    
- Configuration files
    
- Service discovery tools
    

Documentation alone is often insufficient because infrastructure changes over time.

A server may have dependencies that nobody remembers documenting.

This is why technical discovery should be combined with operational knowledge.

---

# 14. Resource Assessment

Virtualization depends on understanding real resource consumption.

Do not size a VM based only on the physical server's installed capacity.

For example:

```
Physical Server
CPU: 32 cores
RAM: 128 GB
```

does not mean:

```
VM
CPU: 32 cores
RAM: 128 GB
```

The physical server may only use:

```
CPU average: 15%
RAM average: 30 GB
```

The VM may therefore need significantly fewer resources.

This is one of the main benefits of capacity planning.

---

# 15. CPU Assessment

CPU assessment should consider more than average utilization.

Important measurements include:

- Average CPU utilization
    
- Peak CPU utilization
    
- Peak duration
    
- CPU ready time where applicable
    
- Load patterns
    
- Number of cores
    
- CPU frequency
    
- Application parallelism
    

For example:

```
Normal:
CPU = 20%

Backup:
CPU = 35%

Monthly report:
CPU = 90% for 45 minutes
```

If you only measure the average, you may miss the workload's important peak behavior.

---

# 16. RAM Assessment

RAM is often more critical than CPU.

Measure:

- Used memory
    
- Available memory
    
- Cache
    
- Swap/pagefile activity
    
- Peak memory
    
- Memory growth
    
- Application memory requirements
    

A workload that appears to need 8 GB today may need 16 GB during peak periods.

Memory pressure can severely affect application performance.

---

# 17. Storage Assessment

Storage assessment includes:

- Capacity
    
- Used space
    
- Growth rate
    
- IOPS
    
- Throughput
    
- Latency
    
- Read/write ratio
    
- Snapshot requirements
    
- Backup requirements
    

For example:

```
Database:
Capacity = 800 GB
Growth = 30 GB/month
IOPS = High
Latency sensitivity = High
```

This workload cannot be sized correctly using capacity alone.

A storage system may have enough terabytes but insufficient IOPS.

---

# 18. Network Assessment

Network assessment should examine:

- Bandwidth
    
- Latency
    
- Packet loss
    
- VLANs
    
- IP addresses
    
- DNS
    
- Routing
    
- Firewall rules
    
- Load balancers
    
- Network dependencies
    
- Traffic peaks
    

For example, migrating a database server to a new network may change its latency to the application server.

Even a small increase in latency can affect database-heavy applications.

---

# 19. Licensing Assessment

Virtualization can change software licensing.

Some applications are licensed by:

- Physical CPU
    
- Virtual CPU
    
- Physical host
    
- VM
    
- Core
    
- User
    
- Instance
    
- Socket
    
- Cluster
    

Therefore, before migration, verify licensing terms.

A technically successful migration can still become a business problem if licensing is violated.

---

# 20. Hardware Dependency Assessment

Some workloads depend on physical hardware.

Examples include:

- USB devices
    
- Specialized PCI devices
    
- Hardware security modules
    
- GPU devices
    
- Physical serial ports
    
- Specialized storage controllers
    
- Hardware dongles
    
- Specialized network interfaces
    

These workloads may require special virtualization features or may be better left physical.

Do not assume that every physical server should be virtualized.

---

# 21. Business Criticality

Not every workload has the same importance.

Classify systems according to business impact.

For example:

```
Tier 1 — Mission Critical
Tier 2 — Important
Tier 3 — Standard
Tier 4 — Non-Critical
```

A database supporting financial transactions might be Tier 1.

A development server might be Tier 4.

Criticality determines:

- Migration order
    
- Testing depth
    
- Downtime tolerance
    
- Backup requirements
    
- Rollback strategy
    
- Approval requirements
    

---

# 22. Recovery Requirements

Two concepts are especially important:

**RTO — Recovery Time Objective**

How quickly must the system be restored?

**RPO — Recovery Point Objective**

How much data loss can the organization tolerate?

For example:

```
RTO = 2 hours
RPO = 15 minutes
```

This means the organization wants the service restored within two hours and can tolerate at most approximately fifteen minutes of data loss.

These requirements influence migration architecture, backup, replication, and rollback.

---

# 23. Compatibility Assessment

Before migrating, verify that the workload is compatible with the target virtualization platform.

Check:

- Operating system version
    
- CPU architecture
    
- Drivers
    
- Boot mode
    
- Firmware
    
- Applications
    
- Databases
    
- Hardware dependencies
    
- Licensing
    
- Security software
    
- Backup agents
    
- Monitoring agents
    

Older operating systems may have compatibility problems with modern virtual hardware.

---

# 24. Security Assessment

Migration changes the security architecture.

Evaluate:

- Firewall rules
    
- VLANs
    
- Network policies
    
- Authentication
    
- Privileged accounts
    
- Encryption
    
- Endpoint protection
    
- Logging
    
- Backup security
    
- Administrative access
    

Do not wait until after migration to think about security.

Security should be part of the design from the beginning.

---

# 25. Phase 2 — Planning the Migration

After assessment, create a migration plan.

The plan should answer:

- What will be migrated?
    
- Why will it be migrated?
    
- Where will it go?
    
- How will it be migrated?
    
- When will it happen?
    
- Who is responsible?
    
- How long will it take?
    
- What could fail?
    
- How will success be measured?
    
- What is the rollback procedure?
    

A good migration plan is specific enough that another qualified engineer can understand the procedure.

---

# 26. Migration Waves

Do not necessarily migrate everything at once.

A safer strategy is to divide workloads into waves.

For example:

```
Wave 1
Development servers

Wave 2
Low-risk internal services

Wave 3
Production application servers

Wave 4
Critical databases
```

This allows the team to learn from earlier migrations.

---

# 27. Pilot Migration

A pilot is a small migration used to validate the process.

Choose a workload that is:

- Representative
    
- Low risk
    
- Easy to restore
    
- Not mission critical
    

The pilot tests:

- Conversion process
    
- Networking
    
- Storage
    
- Drivers
    
- Monitoring
    
- Backup
    
- Performance
    
- Application behavior
    
- Rollback
    

A pilot is not simply a demonstration.

It is a controlled experiment.

---

# 28. Migration Strategy Selection

Different workloads require different strategies.

Common strategies include:

- P2V
    
- Rebuild
    
- Application/data migration
    
- Replication-based migration
    
- Cold migration
    
- Warm migration
    
- Live migration
    
- V2V
    

The correct strategy depends on downtime, compatibility, data size, application architecture, and risk.

---

# 29. Cold Migration

A cold migration stops the source workload before moving it.

Conceptually:

```
Stop Source
    |
Copy / Convert
    |
Start VM
    |
Test
```

The advantage is simplicity and consistency.

The disadvantage is downtime.

Cold migration may be appropriate for systems that can tolerate planned maintenance windows.

---

# 30. Warm Migration

Warm migration attempts to reduce downtime by copying most data while the source system continues operating.

Then a final synchronization occurs during a short cutover.

Conceptually:

```
Source Running
     |
Initial Copy
     |
Source Continues
     |
Final Sync
     |
Short Downtime
     |
Start Target
```

This approach is useful for workloads with limited downtime tolerance.

---

# 31. Live Migration

Live migration moves a running VM between compatible hosts with little or no noticeable downtime.

A simplified conceptual process is:

```
VM Running on Host A
        |
Memory copied
        |
Changed memory copied again
        |
Final synchronization
        |
VM continues on Host B
```

Live migration is generally used between virtualization hosts rather than as a generic method for converting any physical server into a VM.

It depends on compatible CPU, storage, networking, and hypervisor configurations.

---

# 32. Replication-Based Migration

Replication can reduce downtime for large or frequently changing workloads.

The source data is continuously or periodically replicated to the target.

At cutover:

```
Source
  |
  | Replication
  v
Target
  |
Final Sync
  |
Cutover
```

This can be useful for critical workloads.

However, replication does not automatically guarantee application consistency.

Databases and transactional applications may require application-aware replication or coordinated procedures.

---

# 33. Migration Runbook

A **runbook** is a detailed operational procedure.

A migration runbook might look like:

```
1. Confirm change approval.
2. Confirm backup.
3. Confirm backup restore test.
4. Notify stakeholders.
5. Verify target VM resources.
6. Verify network configuration.
7. Begin final synchronization.
8. Stop source workload.
9. Perform final synchronization.
10. Start target VM.
11. Verify operating system.
12. Verify network.
13. Verify application.
14. Verify monitoring.
15. Verify backup.
16. Obtain application-owner validation.
17. Keep rollback option available.
18. Close change after observation period.
```

The important point is that a runbook is operationally executable.

---

# 34. Change Management

Production migration should normally be treated as a controlled change.

A change record should document:

- Purpose
    
- Scope
    
- Risk
    
- Implementation plan
    
- Maintenance window
    
- Dependencies
    
- Validation plan
    
- Rollback plan
    
- Responsible personnel
    
- Communication plan
    

This prevents the migration from becoming an improvised operation.

---

# 35. Communication Planning

Technical teams should communicate with stakeholders.

Depending on the organization, stakeholders may include:

- IT administrators
    
- Network engineers
    
- Security teams
    
- Application owners
    
- Database administrators
    
- Management
    
- Help desk
    
- End users
    

The people who know how an application behaves are often not the people performing the virtualization migration.

Communication closes that knowledge gap.

---

# 36. Migration Dependencies

Migration order should respect dependencies.

Suppose:

```
Web
 |
Application
 |
Database
```

You should not randomly migrate these systems.

A migration plan might first prepare the database, then application infrastructure, then web infrastructure.

Alternatively, if the environment supports parallel migration, multiple components may move together.

The correct order depends on the architecture.

---

# 37. Target Virtualization Architecture

Before migrating workloads, prepare the destination.

The target environment may include:

```
Physical Hosts
      |
Hypervisors
      |
Virtual Switches
      |
Storage
      |
Management
      |
Backup
      |
Monitoring
```

The target must have sufficient:

- CPU
    
- RAM
    
- Storage
    
- Network bandwidth
    
- IOPS
    
- Backup capacity
    
- Management capacity
    

---

# 38. Resource Capacity Planning

Suppose the organization has five workloads.

Their measured requirements are:

```
VM1: 4 vCPU, 8 GB RAM
VM2: 2 vCPU, 4 GB RAM
VM3: 8 vCPU, 16 GB RAM
VM4: 4 vCPU, 8 GB RAM
VM5: 2 vCPU, 4 GB RAM
```

The total allocated resources are:

```
20 vCPU
40 GB RAM
```

But this does not necessarily mean the host requires exactly 20 physical CPU cores.

Virtualization allows controlled overcommitment.

However, overcommitment should be based on real workload behavior.

---

# 39. CPU Overcommitment

CPU overcommitment means assigning more virtual CPU capacity than the host has physical CPU capacity.

For example:

```
Host:
16 physical cores

VMs:
32 total vCPUs
```

This can work if workloads do not all demand maximum CPU simultaneously.

But excessive overcommitment can produce contention and performance problems.

Therefore:

> **Overcommitment is a capacity-planning decision, not free performance.**

---

# 40. Memory Overcommitment

Memory overcommitment can be more dangerous because memory pressure can cause severe performance degradation.

Some platforms support techniques such as:

- Ballooning
    
- Memory compression
    
- Memory sharing
    
- Swapping
    

However, these mechanisms should not be used as a substitute for proper capacity planning.

A workload that constantly experiences memory pressure may become unstable or slow.

---

# 41. Storage Capacity Planning

Storage planning should include more than current disk usage.

Calculate:

```
Current Data
+
Growth
+
Snapshots
+
Backups
+
Replication
+
Free Space
+
Operational Reserve
```

For example:

```
Current:
2 TB

Expected annual growth:
600 GB

Backups:
3 TB

Snapshots / operational reserve:
1 TB

Required capacity:
> 6.6 TB
```

The exact requirement depends on retention and architecture.

---

# 42. Network Planning

The target environment needs appropriate virtual and physical networking.

A common design might separate:

```
Management
Storage
Migration
Production
Backup
```

These networks may be separate VLANs or other logical segments.

The objective is to prevent heavy traffic in one function from interfering with another.

For example, a large backup operation should not unnecessarily saturate the same network used for critical application traffic.

---

# 43. Storage Design

Virtual machines may use:

- Local storage
    
- NAS
    
- SAN
    
- Distributed storage
    
- Ceph
    
- Network filesystems
    
- Other storage backends
    

Storage selection affects:

- Performance
    
- Availability
    
- Migration capability
    
- Backup
    
- Disaster recovery
    
- Cost
    

If the organization wants easy VM mobility between hosts, shared or distributed storage may be particularly useful.

---

# 44. Network Design

The virtual network should be prepared before migration.

Define:

- VLANs
    
- IP ranges
    
- Subnets
    
- Gateways
    
- DNS
    
- DHCP
    
- Routing
    
- Firewall policies
    
- Load balancing
    
- Management access
    

A VM migration should not become the first time anyone discovers that the target VLAN is incorrectly configured.

---

# 45. Security Preparation

Before migration:

- Harden hypervisors
    
- Restrict management access
    
- Configure authentication
    
- Configure MFA where available
    
- Prepare firewall rules
    
- Prepare security monitoring
    
- Protect backups
    
- Verify privileged access
    
- Scan target infrastructure
    

The migration itself should not create a security regression.

---

# 46. Backup Preparation

Before migrating a critical system, verify that a recoverable backup exists.

Do not simply check:

```
Backup job = Successful
```

A successful backup job does not prove that restoration will work.

Perform a restore test when appropriate.

For critical workloads, the migration should have:

```
Source Backup
      +
Target Backup
      +
Rollback Plan
```

---

# 47. The Difference Between Backup and Rollback

These concepts are related but different.

A **backup** is a recoverable copy of data.

A **rollback** is the procedure for returning the service to its previous operational state after a failed change.

For example:

```
Old Physical Server
        |
Migration
        |
New VM
        |
Failure
        |
Rollback
        |
Old Physical Server
```

A backup may help restore data, but it does not necessarily provide an immediate operational rollback.

This distinction is critical in migration planning.

---

# 48. Phase 3 — Executing the Migration

Once planning is complete, execution begins.

A disciplined migration follows the runbook.

The process usually includes:

1. Confirm readiness.
    
2. Confirm backups.
    
3. Confirm maintenance window.
    
4. Verify target infrastructure.
    
5. Perform migration.
    
6. Start target workload.
    
7. Validate.
    
8. Monitor.
    
9. Decide whether to commit or rollback.
    

---

# 49. Pre-Migration Checklist

Before starting:

```
[ ] Change approved
[ ] Stakeholders informed
[ ] Source backup verified
[ ] Rollback procedure ready
[ ] Target capacity confirmed
[ ] Network ready
[ ] Storage ready
[ ] DNS plan ready
[ ] Firewall rules ready
[ ] Monitoring ready
[ ] Application owner available
```

Do not skip this stage because the migration itself may be difficult to stop once started.

---

# 50. Example P2V Workflow

Consider a physical Linux web server.

The high-level process might be:

```
Physical Server
      |
Inventory
      |
Backup
      |
Resource Assessment
      |
Disk Conversion
      |
Create VM
      |
Attach Converted Disk
      |
Adjust Virtual Hardware
      |
Configure Network
      |
Boot VM
      |
Validate
      |
Cutover
```

The exact commands and tools depend on the source OS and target virtualization platform.

---

# 51. Disk Conversion

A P2V process may involve converting a physical disk into a virtual disk.

Conceptually:

```
Physical Disk
      |
Disk Image
      |
Virtual Disk
      |
VM
```

Virtual disk formats can include:

- RAW
    
- QCOW2
    
- VMDK
    
- VHD/VHDX
    

Conversion must preserve data integrity.

After conversion, the VM should be tested before production cutover.

---

# 52. Virtual Hardware Changes

A physical machine may use hardware such as:

```
Physical NIC
Physical SATA/SAS controller
Physical disk
Physical BIOS/UEFI
Physical CPU
```

After virtualization:

```
vNIC
Virtual Storage Controller
Virtual Disk
Virtual Firmware
Virtual CPU
```

The guest operating system may therefore need new drivers or configuration.

This is one reason P2V migrations should always include OS-level validation.

---

# 53. Network Identity During Migration

Network identity is another important issue.

A physical server may have:

- IP address
    
- MAC address
    
- DNS record
    
- Hostname
    
- Certificates
    
- Firewall rules
    

The VM may need to preserve some of these properties.

However, blindly copying network configuration can also cause problems.

For example, a Linux system may retain an old interface configuration tied to the physical NIC.

The new virtual NIC may receive a different interface name.

Therefore, network configuration must be reviewed after conversion.

---

# 54. DNS and Migration

DNS can affect the migration.

Suppose:

```
app.company.local
        |
        v
10.10.10.20
```

The old physical server uses `10.10.10.20`.

If the new VM uses a different IP address, DNS must be updated appropriately.

DNS caching means that the change may not become visible everywhere immediately.

Therefore, DNS TTL and propagation should be considered in the migration plan.

---

# 55. Application Validation

Do not stop validation at:

```
VM boots successfully.
```

A VM can boot perfectly while the application is broken.

Validation should occur at several levels.

### Level 1 — Infrastructure

Is the VM running?

### Level 2 — Operating System

Are services running?

### Level 3 — Network

Can required systems communicate?

### Level 4 — Application

Does the application function?

### Level 5 — Business

Can users perform the required business operation?

---

# 56. Validation Example

Suppose a web application was migrated.

Validation might be:

```
VM powered on
      |
OS boot successful
      |
NIC configured
      |
DNS resolves
      |
Firewall allows traffic
      |
Web server running
      |
Application loads
      |
Database connection works
      |
User login works
      |
Transaction succeeds
```

The final step is important.

A server being "up" does not necessarily mean the business service is operational.

---

# 57. Performance Validation

After migration, compare performance against the baseline.

Measure:

- CPU
    
- RAM
    
- Disk latency
    
- IOPS
    
- Network throughput
    
- Network latency
    
- Application response time
    

For example:

```
Before:
Application response = 150 ms

After:
Application response = 160 ms
```

A small difference may be acceptable.

But:

```
Before:
150 ms

After:
1.8 seconds
```

requires investigation.

---

# 58. Monitoring After Migration

Do not immediately decommission the old physical server.

Keep the migrated workload under observation.

Monitor:

- CPU
    
- Memory
    
- Storage
    
- Network
    
- Application health
    
- Errors
    
- Logs
    
- User reports
    
- Backup jobs
    
- Security events
    

An observation period gives the team time to discover problems that only occur under real workloads.

---

# 59. Rollback Decision

A rollback decision should be based on predefined criteria.

For example:

```
Rollback if:

Critical application failure
OR
Data integrity problem
OR
Performance below agreed threshold
OR
Security control failure
OR
Unrecoverable network problem
```

This is much better than making an emotional decision during an incident.

---

# 60. Rollback Example

Suppose the new VM has serious application problems.

A rollback could be:

```
Stop New VM
     |
Restore/Reactivate Old Server
     |
Restore Network Identity
     |
Verify Application
     |
Notify Users
     |
Investigate Failure
```

The exact procedure depends on the migration architecture.

Rollback must be tested before production migration whenever practical.

---

# 61. Post-Migration Tasks

After successful migration:

- Update documentation
    
- Update monitoring
    
- Update backup policies
    
- Update CMDB/inventory
    
- Verify security controls
    
- Review resource allocation
    
- Confirm DNS
    
- Confirm firewall rules
    
- Update disaster recovery plans
    
- Remove obsolete hardware only after approval
    

Do not immediately delete the old infrastructure.

---

# 62. Decommissioning the Physical Server

Only after the migration is considered stable should the old physical server be retired.

Before decommissioning:

- Confirm application owner approval
    
- Confirm backup
    
- Confirm monitoring
    
- Confirm DNS
    
- Confirm dependencies
    
- Confirm no unexpected traffic
    
- Confirm rollback window has passed
    
- Securely dispose of or repurpose storage
    
- Remove obsolete credentials
    
- Update documentation
    

Physical hardware may contain sensitive data, so storage disposal must follow organizational security requirements.

---

# 63. Migration Risk Management

A simple risk register can help.

|   |   |   |   |
|---|---|---|---|
|Risk|Probability|Impact|Mitigation|
|Application incompatibility|Medium|High|Pilot testing|
|Insufficient CPU|Medium|High|Capacity assessment|
|Insufficient storage IOPS|Medium|High|Performance baseline|
|Network misconfiguration|Medium|High|Pre-migration testing|
|Data corruption|Low|Critical|Verified backup|
|Unexpected downtime|Medium|High|Warm migration|
|Rollback failure|Low/Medium|Critical|Test rollback|
|Licensing issue|Medium|High|License assessment|

The exact values depend on the environment.

---

# 64. Common Migration Risks in Detail

## 64.1 Unknown Dependencies

An application may depend on a server that nobody documented.

For example:

```
Application
     |
     +--> Database
     |
     +--> DNS
     |
     +--> LDAP
     |
     +--> File Share
     |
     +--> Legacy API
```

If the legacy API is forgotten, migration may appear successful until users execute a function that requires it.

---

## 64.2 Wrong Resource Sizing

Over-sizing wastes resources.

Under-sizing causes performance problems.

The solution is measurement.

Use actual utilization data rather than guesses.

---

## 64.3 Storage Bottlenecks

A VM may have enough disk capacity but insufficient storage performance.

For example:

```
Capacity: 5 TB
IOPS: Very Low
```

A database may perform badly despite having plenty of free space.

---

## 64.4 Network Problems

Migration can change:

- MAC addresses
    
- Interface names
    
- VLANs
    
- IP addresses
    
- Routing
    
- Firewall behavior
    

These changes must be tested.

---

## 64.5 Time Synchronization

Virtualized systems depend heavily on correct time.

Incorrect time can cause problems with:

- Kerberos
    
- TLS certificates
    
- Logs
    
- Authentication
    
- Distributed systems
    
- Databases
    

After migration, verify time synchronization.

---

# 65. Migration and Time Synchronization

A virtual machine may receive time from:

- Guest OS NTP
    
- Hypervisor time synchronization
    
- External time service
    

The configuration must be consistent.

Avoid creating confusing situations where multiple mechanisms fight each other.

For domain environments, authentication may fail if time skew becomes too large.

Time is therefore an infrastructure dependency that should be included in migration validation.

---

# 66. Migration and Security Software

Physical servers may have security agents installed.

Examples include:

- Antivirus
    
- EDR
    
- HIDS
    
- Backup agents
    
- Monitoring agents
    

After P2V, these agents may still think they are running on the old physical hardware.

Verify:

- Agent health
    
- Licensing
    
- Hardware identifiers
    
- Network communication
    
- Policy assignment
    

Security monitoring must continue after migration.

---

# 67. Migration and Backup Software

Backup systems may identify workloads by:

- Hostname
    
- IP
    
- UUID
    
- VM identifier
    
- Agent identity
    

After migration, the backup system may need reconfiguration.

Never assume that backup continues automatically simply because the VM is running.

Perform a test backup and, when possible, a test restore.

---

# 68. Migration and Monitoring

Monitoring may depend on:

- IP addresses
    
- Hostnames
    
- Agents
    
- SNMP
    
- APIs
    
- Hypervisor integration
    

A migration can accidentally remove monitoring.

This creates a dangerous situation:

```
VM is running
      |
Monitoring is broken
      |
Failure occurs
      |
Nobody notices
```

Therefore, monitoring validation is part of migration validation.

---

# 69. Migration of Databases

Databases require special planning because data consistency matters.

Important considerations include:

- Database size
    
- Transaction rate
    
- Replication
    
- Backup
    
- Recovery
    
- Storage latency
    
- Application dependencies
    
- Transaction consistency
    

A database migration should not be treated like copying an ordinary file server.

For critical databases, application-aware procedures may be required.

---

# 70. Migration of File Servers

File servers introduce different challenges.

Consider:

- Data volume
    
- File permissions
    
- ACLs
    
- Shares
    
- Open files
    
- User mappings
    
- DNS aliases
    
- Storage performance
    
- Backup
    
- Data growth
    

If permissions are not preserved correctly, the migration can create a security incident even if all files are present.

---

# 71. Migration of Web Servers

Web servers are often easier to migrate because they may be stateless or relatively easy to rebuild.

However, check:

- TLS certificates
    
- DNS
    
- Firewall rules
    
- Load balancer configuration
    
- Application dependencies
    
- Web server modules
    
- Logs
    
- File permissions
    
- External integrations
    

If multiple web servers exist, migration can sometimes be performed gradually.

---

# 72. Migration of Legacy Systems

Legacy applications are often among the most difficult workloads.

They may depend on:

- Old operating systems
    
- Unsupported drivers
    
- Physical hardware
    
- Obsolete software
    
- Static IP addresses
    
- Old protocols
    
- Legacy licensing
    
- Old authentication mechanisms
    

Virtualization can sometimes extend the useful life of such systems by separating them from aging physical hardware.

However, virtualization does not magically make unsupported software secure.

A legacy VM should still be isolated, monitored, and controlled.

---

# 73. Migration and High Availability

Migration may be an opportunity to improve availability.

Instead of:

```
One Physical Server
```

the organization can move toward:

```
Host A       Host B
   \          /
    Virtualization Cluster
          |
         VMs
```

With shared or distributed storage and appropriate networking, workloads may be able to move between hosts.

However, high availability should be designed deliberately.

Simply placing a VM on a cluster does not automatically solve every availability problem.

---

# 74. Migration and Disaster Recovery

Virtualization can simplify disaster recovery.

For example:

```
Primary Site
   |
Virtual Machines
   |
Replication / Backup
   |
Secondary Site
```

A VM can often be replicated or restored more easily than a physical server.

Migration planning should therefore consider whether the new architecture improves the organization's RTO and RPO.

---

# 75. Migration to Proxmox

Proxmox VE is a virtualization platform commonly used for KVM virtual machines and LXC containers.

A migration project involving Proxmox should consider:

- CPU compatibility
    
- VM disk format
    
- Storage backend
    
- Linux guest drivers
    
- Network bridges
    
- VLAN configuration
    
- Backup integration
    
- Cluster architecture
    
- HA requirements
    
- Management access
    

For example, a target environment may use:

```
Proxmox Host
      |
Linux Bridge
      |
VM vNIC
      |
VM
```

The migration must ensure that the VM's network behavior remains correct.

---

# 76. Migration to QEMU/KVM

QEMU/KVM provides hardware-assisted virtualization on Linux.

A migration may involve converting a physical disk into a format suitable for QEMU/KVM and creating a VM around it.

Important considerations include:

- Disk image format
    
- VirtIO drivers
    
- Virtual network interface
    
- Storage controller
    
- Firmware
    
- CPU model
    
- Guest operating system compatibility
    

VirtIO devices often provide efficient paravirtualized I/O, but the guest must support the required drivers.

---

# 77. Migration to VirtualBox

VirtualBox is often used in laboratories, development, and desktop virtualization.

For learning purposes, it can be useful for experimenting with:

- P2V concepts
    
- Disk conversion
    
- Virtual network configuration
    
- VM hardware
    
- Snapshots
    
- Testing
    

However, enterprise migration architecture usually involves platforms designed for centralized management, clustering, production storage, and high availability.

The important lesson is to choose a platform based on requirements rather than familiarity.

---

# 78. Practical Lab — Build a Migration Inventory

Create a small spreadsheet or text file describing your laboratory environment.

For each server, record:

```
Hostname
Operating System
CPU
RAM
Disk
Network
Application
Dependencies
Criticality
Backup
Migration Strategy
```

Example:

```
server01
Linux
4 vCPU
8 GB RAM
100 GB
Production VLAN
Web
Database server
Medium
Yes
P2V
```

This teaches the first professional skill in migration:

**Know what you have before changing it.**

---

# 79. Practical Lab — Measure Resource Utilization

On a Linux server, inspect CPU and memory usage with tools such as:

```
top
```

or:

```
htop
```

Inspect memory:

```
free -h
```

Inspect storage:

```
df -h
```

Inspect block devices:

```
lsblk
```

Inspect network interfaces:

```
ip addr
```

Inspect listening services:

```
ss -tulpn
```

The goal is not simply to run commands.

Interpret what the data means.

---

# 80. Practical Lab — Discover Network Dependencies

Use network tools to understand which systems communicate.

For example:

```
ss -tunap
```

and:

```
ip route
```

You can also examine DNS:

```
resolvectl status
```

or use appropriate DNS lookup tools.

Build a dependency diagram:

```
Client
  |
  v
Web Server
  |
  v
Application
  |
  v
Database
```

Then identify the ports and protocols between each component.

---

# 81. Practical Lab — P2V Concept Demonstration

In a safe laboratory, create a Linux VM.

Treat it as the "physical server."

Record:

- Hostname
    
- IP address
    
- Disk layout
    
- RAM
    
- CPU
    
- Services
    
- Network configuration
    

Then create a second VM representing the migrated system.

Recreate the environment and compare:

```
Before
Physical-style workload

After
Virtual workload
```

The goal is to understand what must remain consistent and what changes during virtualization.

---

# 82. Practical Lab — Migration Runbook

Write a runbook for migrating a test server.

Your runbook should include:

```
Pre-checks
Backup
Migration
Boot
Network validation
Application validation
Performance validation
Monitoring validation
Backup validation
Rollback
Post-migration
```

Do not write:

```
"Check everything."
```

Write specific tests.

For example:

```
Verify DNS resolves server01.example.local.
Verify TCP/443 is reachable.
Verify application login works.
Verify database connection succeeds.
```

A good runbook is measurable.

---

# 83. Practical Lab — Test Rollback

Create a test migration where you intentionally introduce a failure.

For example, change a VM's network configuration incorrectly.

Then practice:

```
Detect failure
     |
Stop target
     |
Restore previous configuration
     |
Restart source/target
     |
Validate service
```

The objective is to understand why rollback must be designed before migration rather than invented during failure.

---

# 84. Practical Lab — Performance Baseline

Measure application performance before migration.

For example:

```
CPU utilization
RAM utilization
Disk latency
Network latency
Application response time
```

Then perform the migration.

Repeat the measurements.

Compare:

```
Before vs After
```

This gives you evidence about whether the migration improved or degraded the system.

---

# 85. Practical Migration Checklist

## Assessment

```
[ ] Inventory completed
[ ] Dependencies identified
[ ] CPU measured
[ ] RAM measured
[ ] Storage measured
[ ] Network measured
[ ] Licensing checked
[ ] Hardware dependencies identified
[ ] Business criticality assigned
[ ] RTO/RPO documented
```

## Planning

```
[ ] Target platform selected
[ ] Target capacity confirmed
[ ] Migration strategy selected
[ ] Migration wave defined
[ ] Pilot completed
[ ] Maintenance window approved
[ ] Runbook written
[ ] Rollback plan written
[ ] Stakeholders informed
```

## Preparation

```
[ ] Backup verified
[ ] Restore tested
[ ] Network prepared
[ ] Storage prepared
[ ] Security prepared
[ ] Monitoring prepared
[ ] DNS prepared
[ ] Application owner available
```

## Execution

```
[ ] Pre-check completed
[ ] Migration started
[ ] Data synchronized
[ ] Target started
[ ] OS validated
[ ] Network validated
[ ] Application validated
[ ] Performance validated
[ ] Monitoring validated
[ ] Backup validated
```

## Post-Migration

```
[ ] Observation period completed
[ ] Documentation updated
[ ] CMDB updated
[ ] Security verified
[ ] Backup verified
[ ] Rollback window closed
[ ] Physical server decommissioned safely
```

---

# 86. Common Migration Mistakes

## Mistake 1 — Migrating Everything at Once

Large migrations should usually be divided into controlled waves.

---

## Mistake 2 — Migrating Without Measuring

Resource allocation based on guesses can produce poor performance.

---

## Mistake 3 — Ignoring Dependencies

Applications are systems, not isolated servers.

---

## Mistake 4 — Treating Boot Success as Application Success

A VM can boot while the application remains broken.

---

## Mistake 5 — Not Testing Backups

A backup job reporting "success" is not the same as a successful restore.

---

## Mistake 6 — No Rollback Plan

If you do not know how to return to the previous state, you do not have a complete migration plan.

---

## Mistake 7 — Decommissioning the Old Server Too Quickly

Keep the source environment available until the migration has passed the defined observation and rollback period.

---

## Mistake 8 — Forgetting Security

Migration can accidentally expose services, change firewall behavior, or break monitoring.

---

## Mistake 9 — Ignoring Licensing

Software licensing rules can change when workloads move to virtualized infrastructure.

---

## Mistake 10 — No Documentation

A migration that works but leaves the organization with inaccurate documentation creates future operational risk.

---

# 87. Migration Decision Tree

When deciding how to migrate a workload, ask:

```
Can the workload be virtualized?
        |
       Yes
        |
Does it depend on physical hardware?
        |
    +---+---+
   Yes      No
    |        |
Special     Continue
handling
             |
Can downtime be tolerated?
             |
        +----+----+
       Yes        No
        |          |
      Cold     Warm/Live/
     Migration Replication
        |          |
        +----+-----+
             |
     Is P2V appropriate?
             |
       +-----+-----+
      Yes          No
       |            |
      P2V        Rebuild/
                 Data Migration
```

This is not a rigid rule.

It is a reasoning framework.

---

# 88. How to Think Like a Migration Engineer

A beginner may ask:

> "How do I convert this server into a VM?"

A migration engineer asks:

> "What is the workload, what does it depend on, what are its business requirements, what is the safest migration strategy, how will I validate it, and how will I recover if it fails?"

That difference is important.

The technical conversion is only one part of the migration.

---

# 89. Example: Complete Migration Scenario

Consider a company with:

```
Physical Server A
Web Application
8 CPU cores
16 GB RAM
300 GB storage

Physical Server B
Database
16 CPU cores
64 GB RAM
1.5 TB storage

Physical Server C
File Server
8 CPU cores
32 GB RAM
3 TB storage
```

The organization wants to consolidate them onto two virtualization hosts.

---

## Step 1 — Assess

Measurements show:

```
Web:
Average CPU = 20%
RAM = 8 GB

Database:
Average CPU = 35%
RAM = 40 GB
High storage I/O

File:
Average CPU = 10%
RAM = 12 GB
High storage capacity
```

This shows that copying physical specifications directly would waste resources.

---

## Step 2 — Identify Dependencies

The web application communicates with the database.

The file server is used by a different group.

Therefore:

```
Web -> Database
```

is a critical dependency.

---

## Step 3 — Design

The organization creates:

```
Host A
  VM Web
  VM Database

Host B
  VM File
```

But this design has a problem.

If Host A fails, both the web and database workloads fail.

A better HA design might distribute critical workloads:

```
Host A
  VM Web
  VM File

Host B
  VM Database
```

depending on resource requirements and the availability architecture.

---

## Step 4 — Prepare

The team configures:

- Storage
    
- Virtual networks
    
- Firewall rules
    
- Backups
    
- Monitoring
    
- Management access
    

---

## Step 5 — Pilot

The file server is migrated first because it has lower business risk.

The team discovers that one firewall rule must be adjusted.

This is exactly why pilots are valuable.

---

## Step 6 — Production Migration

The web and database workloads are migrated using a carefully coordinated process.

The database migration is performed during an approved maintenance window.

---

## Step 7 — Validation

The team verifies:

```
Web server -> Application
Application -> Database
Users -> Web
Backup -> Database
Monitoring -> All VMs
```

---

## Step 8 — Observation

The team monitors performance for the agreed period.

The database storage latency is slightly higher than expected.

The storage configuration is adjusted.

---

## Step 9 — Decommission

Only after successful observation is the old physical hardware retired.

This scenario demonstrates that migration is a lifecycle rather than a single conversion operation.

---

# 90. Exam-Style Questions

## Question 1

What is P2V migration?

### Answer

P2V means Physical-to-Virtual migration. It is the process of moving a physical workload into a virtual machine, often by converting its storage and adapting its operating system to virtual hardware.

---

## Question 2

Why should resource utilization be measured before migration?

### Answer

Because installed physical capacity does not necessarily represent actual workload requirements. Measurements allow the engineer to size the VM appropriately and avoid both over-provisioning and under-provisioning.

---

## Question 3

What is application dependency mapping?

### Answer

It is the process of identifying relationships and communication paths between workloads and services so that migration order and network configuration can preserve application functionality.

---

## Question 4

What is the difference between RTO and RPO?

### Answer

RTO defines the maximum acceptable recovery time. RPO defines the maximum acceptable amount of data loss, expressed as a time interval.

---

## Question 5

Why is a rollback plan necessary?

### Answer

Migration can fail because of compatibility, performance, network, storage, application, or data problems. A rollback plan provides a controlled way to return the service to its previous operational state.

---

## Question 6

Why should a pilot migration be performed?

### Answer

A pilot allows the team to test the migration method, target infrastructure, dependencies, validation procedures, and rollback process on a low-risk workload before migrating critical systems.

---

## Question 7

What is cold migration?

### Answer

Cold migration stops the source workload before copying or converting it. It is generally simpler but requires downtime.

---

## Question 8

What is warm migration?

### Answer

Warm migration performs most of the data transfer while the source continues operating, followed by a final synchronization and short cutover period.

---

## Question 9

Why should the old physical server not immediately be destroyed after P2V?

### Answer

The migrated workload may have hidden dependencies or operational problems that are not discovered immediately. Keeping the source available during the defined rollback/observation period provides a recovery option.

---

## Question 10

Why is a backup not the same as a rollback plan?

### Answer

A backup provides a recoverable copy of data, while rollback is an operational procedure for returning a service to its previous state. A backup may support rollback but does not automatically provide an immediate rollback mechanism.

---

# 91. Scenario-Based Questions

## Scenario 1 — High CPU After Migration

A physical server normally used 20% CPU. After migration, the VM frequently reaches 90%.

What should you investigate?

Consider:

- VM CPU allocation
    
- Host contention
    
- CPU scheduling
    
- Application behavior
    
- Background processes
    
- CPU model/configuration
    
- Workload changes
    
- Host capacity
    

Do not immediately assume that virtualization itself caused the problem.

---

## Scenario 2 — Application Cannot Reach Database

The web VM starts successfully, but users receive database connection errors.

What should you investigate?

Check:

```
DNS
Routing
VLAN
Firewall
IP addresses
Database service
Database listener
Credentials
Application configuration
```

This illustrates why application validation must go beyond checking whether the VM boots.

---

## Scenario 3 — Backup No Longer Works

The VM is running normally, but the backup platform no longer protects it.

What migration step was probably overlooked?

Backup integration should have been included in the migration plan and post-migration validation.

---

## Scenario 4 — Rollback Required

After migration, the application shows data consistency problems.

What should happen?

If the problem meets the predefined rollback criteria, follow the tested rollback procedure rather than improvising.

The incident should then be investigated before attempting another migration.

---

# 92. Final Project — Plan and Execute a Virtualization Migration

Design a complete migration project for a fictional organization.

The organization currently has:

```
Server01
Physical Linux Web Server

Server02
Physical Linux Database Server

Server03
Physical Windows File Server

Server04
Physical Monitoring Server
```

You must migrate the workloads into a virtualized environment.

Your project should include:

### Assessment

Document:

- CPU
    
- RAM
    
- Storage
    
- Network
    
- Applications
    
- Dependencies
    
- Criticality
    
- Licensing
    
- Hardware dependencies
    

### Target Design

Design:

- Hypervisors
    
- CPU capacity
    
- RAM capacity
    
- Storage
    
- Networks
    
- VLANs
    
- Backup
    
- Monitoring
    
- Security
    
- High availability
    

### Migration Strategy

Choose between:

- P2V
    
- Rebuild
    
- Cold migration
    
- Warm migration
    
- Replication
    
- Other appropriate approaches
    

Explain why.

### Migration Waves

Define which workloads migrate first and why.

### Runbook

Write the detailed sequence of operations.

### Validation

Define tests for:

- Infrastructure
    
- Operating system
    
- Network
    
- Application
    
- Database
    
- Users
    
- Backup
    
- Monitoring
    
- Security
    

### Rollback

Define:

- Rollback triggers
    
- Rollback procedure
    
- Required resources
    
- Decision authority
    

### Post-Migration

Define:

- Observation period
    
- Documentation updates
    
- Backup verification
    
- Monitoring verification
    
- Decommissioning process
    

If you can design this project coherently, you understand the essential principles of virtualization migration.

---

# 93. Final Mental Model

The entire chapter can be reduced to one lifecycle:

```
                    MIGRATION

                       |
                       v
                 1. ASSESS
                       |
          What do we have?
          How does it behave?
          What depends on it?
                       |
                       v
                 2. PLAN
                       |
          Where should it go?
          How should it move?
          What can go wrong?
                       |
                       v
                 3. PREPARE
                       |
          Target infrastructure
          Backup
          Network
          Security
          Monitoring
          Rollback
                       |
                       v
                 4. PILOT
                       |
             Test on low-risk
                 workload
                       |
                       v
                 5. EXECUTE
                       |
              Perform migration
                       |
                       v
                 6. VALIDATE
                       |
          Does the infrastructure work?
          Does the application work?
          Does the business work?
                       |
                 +-----+-----+
                 |           |
              Success      Failure
                 |           |
                 v           v
              Monitor     Rollback
                 |           |
                 v           v
             Commit      Investigate
                 |
                 v
            Decommission
```

The critical concept is that **migration is not complete when the VM starts**.

Migration is complete when the workload is operational, secure, performant, monitored, backed up, documented, and accepted by the business.

---

# 94. Glossary

**P2V** — Physical-to-Virtual migration.

**V2V** — Virtual-to-Virtual migration.

**Migration** — The process of moving a workload from one infrastructure environment to another.

**P2P** — Physical-to-Physical migration, generally involving movement between physical systems.

**Cold Migration** — Migration performed while the source workload is stopped.

**Warm Migration** — Migration in which most data is transferred while the source remains active, followed by a final synchronization and cutover.

**Live Migration** — Moving a running VM between compatible hosts with minimal service interruption.

**Cutover** — The point at which production traffic or users are directed to the new environment.

**Rollback** — Returning a workload to its previous operational state after a failed or unacceptable change.

**Runbook** — A detailed operational procedure for performing a technical task.

**Pilot** — A controlled test migration performed before larger production migrations.

**Migration Wave** — A group of workloads migrated during a planned phase.

**Dependency** — A service, system, network, storage, identity, or external component required by a workload.

**Capacity Planning** — Determining whether infrastructure has sufficient resources for current and future workloads.

**Overcommitment** — Assigning more virtual resources than the physical infrastructure can simultaneously provide, based on expected workload behavior.

**CPU Contention** — Competition between workloads for available processor resources.

**IOPS** — Input/output operations per second, a common storage performance measurement.

**Latency** — The time required for an operation or communication to complete.

**Throughput** — The amount of data or work processed during a given period.

**RTO** — Recovery Time Objective.

**RPO** — Recovery Point Objective.

**P2V Tool** — Software used to convert or migrate a physical system into a virtual machine.

**Application Dependency Mapping** — Identifying communication and functional dependencies between workloads.

**Change Management** — A controlled process for planning, approving, implementing, and documenting infrastructure changes.

**Cutover Window** — The scheduled period during which the final migration and service transition occurs.

**Baseline** — A set of measurements representing normal system behavior before a change.

**Configuration Drift** — The gradual difference between intended and actual system configuration.

**Decommissioning** — The controlled retirement of an old system after migration.

**Migration Runbook** — A step-by-step operational document used to execute a migration.

---

# 95. Final Knowledge Checklist

Before considering this chapter mastered, you should be able to explain:

- What migration to virtualization means.
    
- Why organizations migrate to virtualization.
    
- The difference between virtualization, migration, and modernization.
    
- What P2V means.
    
- What V2V means.
    
- When P2V is appropriate.
    
- When rebuilding a VM may be better than P2V.
    
- Why physical resource utilization must be measured.
    
- How to assess CPU usage.
    
- How to assess memory requirements.
    
- Why storage capacity alone is insufficient.
    
- Why storage IOPS and latency matter.
    
- How to assess network dependencies.
    
- Why application dependency mapping matters.
    
- How licensing can affect migration.
    
- Why some hardware-dependent workloads may be difficult to virtualize.
    
- How business criticality affects migration planning.
    
- What RTO and RPO mean.
    
- How to evaluate compatibility.
    
- Why security must be included from the beginning.
    
- What a migration wave is.
    
- Why pilot migrations are valuable.
    
- The difference between cold and warm migration.
    
- What live migration means.
    
- What replication-based migration means.
    
- Why a migration runbook is useful.
    
- Why change management matters.
    
- How to plan the target virtualization architecture.
    
- How to calculate basic resource requirements.
    
- What CPU and memory overcommitment mean.
    
- How to plan storage capacity.
    
- How to prepare virtual networks.
    
- Why backups must be verified and restored during testing.
    
- The difference between backup and rollback.
    
- How to execute a controlled migration.
    
- How to validate the operating system.
    
- How to validate networking.
    
- How to validate applications.
    
- How to validate business functionality.
    
- How to perform performance comparisons.
    
- Why monitoring must be verified after migration.
    
- How to define rollback criteria.
    
- Why the old physical system should remain available during the rollback window.
    
- How to safely decommission old infrastructure.
    
- How to handle databases, file servers, web servers, and legacy systems.
    
- How virtualization migration can support high availability and disaster recovery.
    
- How to approach a complete migration project from assessment through decommissioning.
    

The professional mindset to retain is:

> **Measure first. Understand dependencies. Plan the target. Test the migration. Protect the rollback path. Validate the business service. Only then commit the change.**

That mindset is more important than any individual migration tool.