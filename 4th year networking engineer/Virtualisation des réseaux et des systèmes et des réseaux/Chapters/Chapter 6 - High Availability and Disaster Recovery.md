# Chapter 6 — High Availability and Disaster Recovery

## Introduction

A virtualized or containerized infrastructure is valuable only if the services running on it remain available when users need them. In a laboratory environment, shutting down a server may be a minor inconvenience. In a production environment, the same event can interrupt business operations, prevent users from accessing applications, cause financial losses, or damage the reputation of an organization.

This leads to two closely related disciplines:

**High Availability (HA)** focuses on keeping services available when components fail.

**Disaster Recovery (DR)** focuses on restoring services and data after serious failures, including failures that cannot be solved simply by restarting a workload.

These concepts are related, but they are not interchangeable.

A high-availability platform may automatically restart a virtual machine on another host after a server failure. That is useful for service continuity. However, if a storage system is destroyed, an entire data center becomes unavailable, or data is accidentally deleted, automatic VM restart is not enough. A separate recovery strategy is required.

This chapter develops a practical understanding of availability, automatic failover, backup, restoration, and disaster recovery. The goal is not merely to memorize technologies or commands. The goal is to understand how to design systems that can tolerate failure and how to recover them when failure exceeds the protection provided by high availability.

---

# 1. Learning Objectives

By the end of this chapter, you should be able to explain what high availability means and distinguish it from fault tolerance, backup, and disaster recovery.

You should understand common causes of infrastructure failure and identify the difference between a single point of failure and a redundant component.

You should understand the concepts of **SPOF, redundancy, failover, failback, RPO, RTO, MTBF, and MTTR**.

You should be able to explain how automatic failover works in a virtualized cluster.

You should understand the role of shared storage, replicated storage, cluster membership, quorum, fencing, and health checks in HA systems.

You should understand why automatic failover cannot replace backups.

You should be able to design a basic backup strategy, understand full, incremental, and differential backups, and explain the 3-2-1 backup principle.

You should be able to perform and validate a restoration rather than simply creating backups.

Finally, you should be able to reason about availability and recovery using realistic failure scenarios.

---

# 2. Availability as a Systems Concept

Availability describes whether a service is operational and accessible when it is expected to be available.

A simple model is:

```
Availability =
Uptime / (Uptime + Downtime)
```

For example, if a service is available for 99 hours and unavailable for 1 hour:

```
Availability = 99 / 100
            = 99%
```

In real systems, availability is often expressed as a percentage:

```
99%
99.9%
99.99%
99.999%
```

The additional "9s" represent increasingly strict availability requirements.

However, availability is not just a mathematical percentage. A system can technically be "up" while still being unusable because requests are extremely slow, authentication is broken, or an essential dependency has failed.

Therefore, availability should be defined in terms of the actual service users require.

---

# 3. The Availability Spectrum

It is useful to think of infrastructure as a spectrum.

At one extreme:

```
Single Server
Single Disk
Single Network Path
Single Power Supply
Single Copy of Data
```

This design has many potential failure points.

At the other extreme:

```
Multiple Hosts
Redundant Storage
Redundant Network Paths
Redundant Power
Multiple Copies of Data
Geographically Separate Recovery
```

The second architecture can tolerate significantly more failures, but it is also more expensive and complex.

The engineering question is therefore not:

> "How can I make the system impossible to break?"

That is unrealistic.

The better question is:

> "Which failures must the system survive automatically, and which failures must be recovered through a deliberate disaster-recovery process?"

---

# 4. Failure Is Normal

A reliable infrastructure design assumes that components will eventually fail.

Examples include:

```
Physical server failure
CPU or RAM failure
Disk failure
RAID controller failure
Power failure
Network switch failure
Network link failure
Storage failure
Hypervisor crash
Operating-system failure
Virtual machine crash
Container crash
Application failure
Human error
Malware or ransomware
Accidental deletion
Configuration error
Data corruption
Natural disaster
Data-center outage
```

The purpose of HA and DR is not to eliminate these events.

The purpose is to limit their consequences.

---

# 5. Single Point of Failure

A **Single Point of Failure (SPOF)** is a component whose failure can make the entire service unavailable.

Consider:

```
              Users
                │
                ▼
          Single Switch
                │
         ┌──────┴──────┐
         ▼             ▼
       Server A      Server B
```

If both servers depend on one switch and the switch fails:

```
Switch failure
      ↓
Server A inaccessible
Server B inaccessible
      ↓
Service unavailable
```

The servers themselves are redundant, but the network is not.

The switch is therefore a SPOF.

---

# 6. Removing Single Points of Failure

To remove the network SPOF, the architecture might become:

```
             Users
            /     \
           ▼       ▼
       Switch A  Switch B
          │         │
          └────┬────┘
               │
        ┌──────┴──────┐
        ▼             ▼
      Server A      Server B
```

Now one switch can fail while connectivity remains through the other path, assuming the network is correctly configured.

This demonstrates a fundamental HA principle:

> Redundancy is useful only when the redundant components do not share the same failure domain.

Two power supplies connected to the same failed power distribution system may not provide meaningful redundancy.

Two servers connected to the same failed switch may still lose network connectivity.

Two copies of data stored on the same failed storage system may not provide disaster recovery.

---

# 7. Redundancy

Redundancy means having additional components or copies that can provide service when another component fails.

Examples include:

```
Two servers
Two network links
Two switches
Two power supplies
Multiple disks
Multiple storage systems
Multiple application replicas
Multiple backup copies
```

Redundancy can exist at different layers:

```
Hardware
Operating System
Virtualization
Storage
Network
Application
Data
Geography
```

An effective HA design considers the complete service path rather than only one component.

---

# 8. High Availability

High Availability is the design and operation of systems intended to minimize service interruption when failures occur.

A basic HA architecture may look like:

```
                  Users
                    │
                    ▼
               Load Balancer
                 /       \
                ▼         ▼
             Server A   Server B
                │         │
                └────┬────┘
                     ▼
                Shared Data
```

If Server A fails:

```
Server A → FAILED

Load Balancer
      ↓
Server B
      ↓
Users continue receiving service
```

The service may experience a brief interruption or degraded capacity, but the objective is to avoid a complete outage.

---

# 9. Failover

**Failover** is the process of moving service responsibility from a failed component to another available component.

For example:

```
Normal:
Primary Server
      ↓
Application

Failure:
Primary Server ✕
      ↓
Secondary Server
      ↓
Application
```

Failover can be:

```
Manual
Automatic
Semi-automatic
```

Automatic failover is particularly valuable when rapid recovery is required.

---

# 10. Failback

After a failure is resolved, the service may be returned to the original component.

This is called **failback**.

For example:

```
Normal:
Host A → Active
Host B → Standby

Failure:
Host A → Failed
Host B → Active

Recovery:
Host A → Repaired

Failback:
Host A → Active
Host B → Standby
```

Failback should be planned carefully.

Immediately moving workloads back to a repaired server can create unnecessary risk if the root cause of the original failure has not been understood.

---

# 11. Automatic Failover

Automatic failover requires a system that can detect failure and make a decision about where the workload should run next.

A simplified process is:

```
Health Monitoring
       ↓
Failure Detected
       ↓
Cluster Decision
       ↓
Fencing / Isolation
       ↓
Select Healthy Target
       ↓
Start Workload
       ↓
Verify Health
       ↓
Restore Service
```

Every step matters.

If failure detection is incorrect, the system can make a dangerous decision.

If the failed host is not properly isolated, two systems may attempt to operate the same workload simultaneously.

---

# 12. Failure Detection

A cluster needs a mechanism for determining whether a node is healthy.

Possible mechanisms include:

```
Heartbeat
Network reachability
Agent health
Service health checks
Storage connectivity
Application probes
```

A simple heartbeat model is:

```
Node A ── heartbeat ──> Cluster
Node B ── heartbeat ──> Cluster
Node C ── heartbeat ──> Cluster
```

If Node A stops sending heartbeats:

```
Node A
   X
No heartbeat
   ↓
Cluster suspects failure
```

However, a missing heartbeat does not necessarily prove that the server has failed.

The network path itself may have failed.

---

# 13. The Split-Brain Problem

One of the most important HA concepts is **split brain**.

Imagine a two-node cluster:

```
Node A  <──── network ────>  Node B
```

The network connection fails.

Now each node may believe the other is unavailable:

```
Node A:
"Node B is down."

Node B:
"Node A is down."
```

If both nodes independently activate the same workload or modify shared state, the system can become inconsistent.

This is:

```
Split Brain
```

Split brain can cause severe data corruption.

Therefore, HA systems need mechanisms to prevent multiple nodes from simultaneously assuming control of the same resources.

---

# 14. Quorum

**Quorum** is a mechanism used by clustered systems to determine whether enough cluster members agree that an operation is safe.

In a simple cluster, the principle is:

```
Majority agreement
```

For example, in a three-node cluster:

```
Node A
Node B
Node C
```

a majority is:

```
2 out of 3
```

If one node disappears, the remaining two can still form a majority.

In a two-node system, quorum is more complicated because one node represents only 50% of the cluster.

This is one reason production HA clusters often use an odd number of voting members or an additional quorum mechanism.

---

# 15. Fencing

Fencing is the process of preventing a failed or untrusted node from accessing resources before another node takes control.

The fundamental principle is:

> Make sure the old owner cannot continue operating before allowing a new owner to take over.

A simplified sequence is:

```
Node A suspected failed
        ↓
Fence Node A
        ↓
Node A cannot access shared resources
        ↓
Node B takes ownership
        ↓
Workload starts on Node B
```

Fencing can involve mechanisms such as:

```
Power management
Out-of-band management
Storage-level fencing
Network isolation
Cluster-specific fencing mechanisms
```

The exact implementation depends on the platform.

---

# 16. Why Fencing Matters

Suppose a virtual machine is running on Host A.

The cluster thinks Host A has failed.

Host B attempts to start the same VM.

But Host A is actually still running and has simply lost network communication.

Now:

```
Host A → VM
Host B → Same VM
```

If both access the same virtual disk without proper coordination, severe corruption can occur.

Fencing prevents this dangerous situation.

Therefore:

> Automatic failover without reliable fencing can be more dangerous than no automatic failover at all.

---

# 17. Virtualization and High Availability

Virtualization makes HA particularly powerful because a virtual machine is abstracted from the physical hardware.

Suppose:

```
Host A
   │
   └── VM Web
```

If Host A fails, a cluster can potentially start the VM on Host B:

```
Host A ✕
   │
   X
   │
Host B
   │
   └── VM Web
```

The guest operating system does not necessarily need to be redesigned.

This is one of the major operational advantages of server virtualization.

---

# 18. VM Failover

A simplified HA virtualization workflow is:

```
VM running on Host A
        ↓
Host A fails
        ↓
Cluster detects failure
        ↓
Host A is fenced if required
        ↓
Cluster selects Host B
        ↓
VM starts on Host B
        ↓
VM services become available
```

The VM must have access to its required storage and network configuration.

Therefore, VM HA depends on more than the hypervisor alone.

---

# 19. Shared Storage

Traditional VM HA often relies on shared storage.

For example:

```
             Shared Storage
             /            \
            /              \
       Host A              Host B
          │                   │
          └────── VM ─────────┘
```

Both hosts can access the VM's virtual disk.

If Host A fails:

```
Host B
   ↓
Access VM disk
   ↓
Start VM
```

Common shared storage technologies include:

```
NFS
iSCSI
Fibre Channel
Distributed storage systems
```

The exact technology depends on the virtualization platform and environment.

---

# 20. Shared Storage Is Not Automatically Highly Available

Consider:

```
Host A ──┐
         ├── Storage System
Host B ──┘
```

If the storage system itself fails:

```
Storage ✕
    ↓
Host A cannot access VM disks
Host B cannot access VM disks
    ↓
VMs unavailable
```

The storage system is now a SPOF.

Therefore, an HA design must evaluate storage redundancy as well as compute redundancy.

---

# 21. Distributed Storage

Some virtualization platforms use distributed storage.

Instead of:

```
Host A ──> Central Storage
Host B ──> Central Storage
Host C ──> Central Storage
```

data can be distributed across multiple nodes:

```
Node A ─┐
Node B ─┼── Distributed Storage
Node C ─┘
```

Data may be replicated across nodes.

If one node fails, other nodes can potentially provide the required data.

This removes some centralized storage dependencies, but introduces additional complexity.

---

# 22. Proxmox VE and HA

Proxmox VE provides cluster and HA functionality for virtual machines and containers.

A simplified architecture is:

```
             Proxmox Cluster
        ┌────────┼────────┐
        ▼        ▼        ▼
      Node A   Node B   Node C
        │        │        │
       VM       VM       CT
```

The cluster can manage workloads across nodes.

Proxmox HA can automatically recover managed resources on another available node when a node fails, provided the cluster and storage architecture satisfy the necessary requirements.

The exact behavior depends on the configuration.

---

# 23. VirtualBox and HA

VirtualBox is extremely useful for learning virtualization, but it is not normally the platform you would choose to build a production multi-node HA cluster.

It is primarily a desktop virtualization solution.

You can, however, use VirtualBox to build a laboratory environment:

```
Physical Computer
        │
        ├── VM 1 → Node A
        ├── VM 2 → Node B
        └── VM 3 → Node C
```

This can be useful for studying cluster concepts.

The important distinction is:

```
VirtualBox
→ Lab virtualization

Enterprise HA platform
→ Production cluster management
```

---

# 24. KVM and HA

KVM is a Linux kernel virtualization technology.

A KVM-based virtualization environment can be combined with cluster management, shared storage, distributed storage, and HA tooling.

The conceptual architecture is:

```
Linux
  ↓
KVM
  ↓
Virtual Machines
  ↓
Cluster Management
  ↓
HA / Failover
```

KVM itself is not the entire HA solution.

This distinction is important.

---

# 25. Container Orchestration and High Availability

Kubernetes provides another model for availability.

Instead of treating a VM as the unit of recovery, Kubernetes manages Pods.

For example:

```
Deployment
replicas = 3

Pod A → Node 1
Pod B → Node 2
Pod C → Node 3
```

If Node 2 fails:

```
Pod B lost
     ↓
Deployment/ReplicaSet detects missing replica
     ↓
Replacement Pod
     ↓
Scheduled on another available node
```

This is application-level orchestration rather than traditional VM failover.

---

# 26. VM HA Versus Container Orchestration

The two models solve related but different problems.

VM HA:

```
Physical host failure
        ↓
Restart VM elsewhere
```

Container orchestration:

```
Workload instance failure
        ↓
Create replacement Pod
```

VM HA preserves the virtual machine as the recovery unit.

Kubernetes generally treats Pods as disposable workload instances.

The distinction is:

```
VM-oriented HA
→ Infrastructure recovery

Container orchestration
→ Application workload reconciliation
```

A production architecture may use both.

---

# 27. Failure Domains

Failure domains are areas that can fail together.

Examples:

```
Disk
Server
Rack
Power circuit
Network switch
Availability zone
Data center
Region
```

Suppose you run:

```
Replica A → Rack 1
Replica B → Rack 1
Replica C → Rack 1
```

All three can fail if Rack 1 loses power.

A better design may be:

```
Replica A → Rack 1
Replica B → Rack 2
Replica C → Rack 3
```

The exact distribution depends on the infrastructure.

Availability design should always ask:

> What components can fail together?

---

# 28. Backup Versus Replication

Replication and backup are not the same thing.

Replication usually means:

```
Data changes
      ↓
Copies updated quickly
```

If data is accidentally deleted:

```
Delete on Primary
      ↓
Replication
      ↓
Delete on Replica
```

The replica may faithfully reproduce the mistake.

A backup provides a historical recovery point.

For example:

```
Monday backup
Tuesday backup
Wednesday backup
```

If data is deleted Wednesday:

```
Restore Tuesday backup
```

This is why replication cannot replace backup.

---

# 29. Backup as a Recovery Mechanism

A backup is a separate copy of data or system state that can be used to recover after loss or corruption.

Backup targets can include:

```
Virtual machine images
Virtual disks
Application data
Databases
Configuration
Container manifests
Kubernetes resources
Files
System state
```

A useful backup strategy considers what exactly needs to be restored.

---

# 30. What Should Be Backed Up?

Do not assume that backing up the VM is automatically enough.

Consider:

```
Operating system
Application configuration
Application data
Database
Secrets
Network configuration
Virtual machine configuration
Kubernetes manifests
Persistent volumes
External dependencies
```

The appropriate backup scope depends on the application.

For a stateless Kubernetes frontend, backing up the Deployment manifest may be more important than backing up the Pod itself.

For a database, the data is critical.

---

# 31. Full Backup

A full backup copies all selected data.

For example:

```
Sunday:
Full backup → 500 GB
```

Advantages:

```
Simple restoration
Self-contained backup set
Easy to understand
```

Disadvantages:

```
Large storage requirement
Longer backup window
Higher network and I/O consumption
```

---

# 32. Incremental Backup

An incremental backup stores changes since the previous backup of any type.

Example:

```
Sunday:
Full = 500 GB

Monday:
Incremental = 10 GB

Tuesday:
Incremental = 8 GB

Wednesday:
Incremental = 12 GB
```

To restore Wednesday, you generally need:

```
Full backup
+
Monday incremental
+
Tuesday incremental
+
Wednesday incremental
```

Advantages include smaller daily backups.

The disadvantage is a more complex restoration chain.

---

# 33. Differential Backup

A differential backup stores changes since the last full backup.

Example:

```
Sunday:
Full = 500 GB

Monday:
Differential = 10 GB

Tuesday:
Differential = 18 GB

Wednesday:
Differential = 30 GB
```

To restore Wednesday, you generally need:

```
Sunday Full
+
Wednesday Differential
```

This makes restoration simpler than a long incremental chain, but differential backups grow over time until the next full backup.

---

# 34. Backup Comparison

A simple comparison is:

```
Full:
Every backup → all selected data

Incremental:
Backup → changes since previous backup

Differential:
Backup → changes since last full
```

The correct strategy depends on:

```
Data volume
Change rate
Backup window
Storage capacity
Network bandwidth
Recovery requirements
```

---

# 35. Recovery Point Objective

**RPO — Recovery Point Objective** describes how much recent data loss the organization can tolerate.

Suppose:

```
RPO = 1 hour
```

This means the recovery design should aim to limit data loss to approximately one hour or less.

If backups are taken every 24 hours:

```
Potential data loss ≈ 24 hours
```

That does not meet a one-hour RPO.

RPO is therefore closely connected to backup frequency and replication strategy.

---

# 36. Recovery Time Objective

**RTO — Recovery Time Objective** describes how quickly a service must be restored after an outage.

For example:

```
RTO = 30 minutes
```

The organization expects the service to be restored within approximately 30 minutes.

A backup strategy that requires:

```
12 hours to restore
```

does not meet that RTO.

RTO therefore affects:

```
Backup technology
Recovery automation
Infrastructure design
Spare capacity
Failover architecture
```

---

# 37. RPO and RTO Together

These two objectives answer different questions:

```
RPO:
How much data can we lose?

RTO:
How long can the service be unavailable?
```

For example:

```
RPO = 15 minutes
RTO = 1 hour
```

The system should aim to:

```
Lose no more than ~15 minutes of data
Restore service within ~1 hour
```

These values should come from business requirements, not arbitrary technical preferences.

---

# 38. Backup Frequency and RPO

Suppose:

```
Backup frequency = every 6 hours
```

Then, under ordinary assumptions, the maximum data-loss window can approach six hours.

If the business requires:

```
RPO = 30 minutes
```

ordinary six-hour backups are insufficient.

You may need:

```
More frequent backups
Continuous replication
Database transaction-log backups
Snapshots combined with backups
Application-specific recovery mechanisms
```

The exact solution depends on the workload.

---

# 39. RTO and Restoration Speed

Suppose a VM is:

```
1 TB
```

and restoration bandwidth is:

```
100 MB/s
```

A raw transfer of 1 TB requires roughly:

```
1,000,000 MB / 100 MB/s
= 10,000 seconds
≈ 2.8 hours
```

Real restoration takes longer because of:

```
Network overhead
Storage performance
Verification
Boot time
Application startup
Database recovery
Manual procedures
```

Therefore, if the required RTO is 30 minutes, simply possessing a backup is not enough.

The recovery process must be engineered and tested.

---

# 40. MTTR

**Mean Time To Repair (MTTR)** is the average time required to repair or restore a failed component or service.

Lower MTTR generally improves availability.

For example:

```
Failure detected → 5 minutes
Diagnosis → 10 minutes
Repair → 30 minutes
Validation → 10 minutes

Total = 55 minutes
```

Automation can reduce several parts of this process.

HA can reduce service interruption without requiring manual repair before service restoration.

---

# 41. MTBF

**Mean Time Between Failures (MTBF)** represents the average time between failures of a system or component.

A simplified availability relationship is sometimes expressed as:

```
Availability ≈ MTBF / (MTBF + MTTR)
```

This illustrates an important point:

You can improve availability by:

```
Increasing time between failures
```

or:

```
Reducing recovery time
```

HA primarily helps reduce effective service interruption.

---

# 42. The 3-2-1 Backup Rule

A widely used backup principle is the **3-2-1 rule**:

```
3 copies of the data
2 different storage media or systems
1 copy off-site
```

For example:

```
Production VM
     │
     ├── Primary storage
     ├── Local backup repository
     └── Off-site backup
```

This reduces the risk that one incident destroys every copy.

---

# 43. Why Off-Site Backups Matter

Imagine:

```
Production server
Backup server
```

both located in the same data center.

A major incident occurs:

```
Fire
Flood
Power disaster
Physical destruction
```

Both copies can be lost.

An off-site copy introduces geographic separation:

```
Site A
Production + Local Backup

       ↓

Site B
Off-site Backup
```

The separation distance should reflect the organization's disaster scenarios.

---

# 44. Offline and Immutable Backups

Modern threats such as ransomware make backup isolation increasingly important.

If a backup repository is continuously writable from the production environment, an attacker who compromises production may also attempt to encrypt or delete backups.

Protection mechanisms can include:

```
Offline backups
Immutable backups
Object-lock retention
Restricted backup credentials
Separate management networks
Multi-factor authentication
Administrative separation
```

The exact implementation depends on the backup platform.

The principle is:

> A backup that an attacker can easily delete may not be a reliable recovery copy.

---

# 45. Backup Encryption

Backups can contain extremely sensitive information.

Therefore, backup data should generally be protected:

```
At rest
In transit
During access
```

Encryption at rest protects stored backups.

Encryption in transit protects data while it moves between:

```
Production
      ↓
Backup repository
```

Access control determines who can restore or retrieve the data.

---

# 46. Backup Retention

Retention determines how long backups are kept.

For example:

```
Daily backups → 30 days
Weekly backups → 12 weeks
Monthly backups → 12 months
```

Retention should consider:

```
Business requirements
Legal requirements
Compliance
Storage cost
Recovery scenarios
```

A backup from yesterday is not sufficient if you discover corruption that began three months ago.

Longer retention provides historical recovery points.

---

# 47. Backup Consistency

A backup must be usable.

Consider a database VM.

If files are copied while the database is actively changing, the resulting copy may not represent a consistent database state depending on the technology and backup method.

Consistency mechanisms can include:

```
Application-aware backup
Database-native backup
Filesystem snapshots
Quiescing
Transaction-log backups
```

The key principle is:

> A backup is useful only if the restored application can return to a valid state.

---

# 48. Crash-Consistent Versus Application-Consistent

A crash-consistent backup is approximately equivalent to the state of a machine after an unexpected power failure.

An application-consistent backup coordinates with the application to produce a more controlled recovery point.

For example:

```
Application-consistent:
Database flushes or coordinates state
       ↓
Backup
       ↓
Predictable recovery
```

The appropriate method depends on the application and backup platform.

---

# 49. VM Snapshots Versus Backups

A VM snapshot is not automatically a backup.

A snapshot usually records a point-in-time state associated with a storage system.

It can be useful for:

```
Testing
Short-term rollback
Maintenance
Development
```

But relying exclusively on snapshots for disaster recovery can be dangerous.

A snapshot may remain dependent on:

```
The same storage system
The same virtualization environment
The same failure domain
```

A backup should generally be maintained independently enough to survive the failure scenario it is intended to protect against.

---

# 50. Snapshot Example

Suppose:

```
VM
 ↓
Storage Array
 ↓
Snapshot
```

If the storage array is destroyed:

```
VM ✕
Snapshot ✕
```

The snapshot did not protect against that failure.

An independent backup might be:

```
VM
 ↓
Backup Repository
 ↓
Off-site Copy
```

This is why snapshots and backups should not be treated as identical concepts.

---

# 51. Backup of Virtual Machines

A VM backup generally captures enough information to restore the VM.

Depending on the platform, this may include:

```
VM configuration
Virtual disks
Metadata
Snapshots or backup state
```

The exact format differs by virtualization platform.

A good VM backup strategy should also consider:

```
Application consistency
Backup frequency
Retention
Encryption
Off-site copies
Restore testing
```

---

# 52. Backup of Containers

Containers are generally treated as ephemeral.

You should not normally think:

```
Back up every running container
```

Instead, identify what is persistent.

For a containerized application:

```
Container image
       +
Configuration
       +
Secrets
       +
Persistent volumes
       +
Database
```

The image may be reproducible from a registry.

The important backup targets are usually:

```
Persistent data
Configuration
Deployment definitions
Application state
```

---

# 53. Kubernetes Backup

Kubernetes environments contain multiple categories of information.

You may need to protect:

```
Cluster configuration
Kubernetes resource definitions
Persistent volumes
Secrets
Application manifests
Custom resources
Databases
External dependencies
```

Backing up only Kubernetes object definitions is not necessarily enough if the application stores important data in Persistent Volumes.

The backup strategy must follow the application's state.

---

# 54. Backup Strategy for a Kubernetes Application

Consider:

```
Frontend Deployment
API Deployment
Database
Persistent Volume
ConfigMaps
Secrets
```

A recovery design might require:

```
Container images
Deployment manifests
Service manifests
ConfigMaps
Secrets
Database backups
Persistent volume backups
```

The correct restoration order may matter.

For example:

```
Storage
   ↓
Database
   ↓
Application
   ↓
Service
```

The actual sequence depends on the application architecture.

---

# 55. Backup Repository Design

A backup repository should be treated as an important infrastructure system.

Consider:

```
Production
     │
     ▼
Backup Repository
     │
     ├── Access Control
     ├── Encryption
     ├── Retention
     ├── Monitoring
     └── Replication / Off-site Copy
```

The backup system itself needs:

```
Monitoring
Capacity planning
Security
Maintenance
Recovery testing
```

If nobody notices that backups have failed for three months, the organization may discover the problem only during a disaster.

---

# 56. Backup Monitoring

A backup strategy should generate operational evidence.

Monitor:

```
Last successful backup
Backup duration
Backup size
Failure rate
Repository capacity
Retention status
Replication status
Restore test results
```

A useful rule is:

> Do not measure whether a backup job ran; measure whether recoverable data exists.

A job can report success while a configuration mistake prevents the intended data from being protected.

---

# 57. Restore Testing

The most important backup test is restoration.

A backup is an assumption until it has been restored successfully.

A restore test might look like:

```
Select backup
      ↓
Restore into isolated environment
      ↓
Boot system
      ↓
Verify filesystem
      ↓
Start application
      ↓
Verify database
      ↓
Test application functionality
      ↓
Record recovery time
```

This validates both:

```
RPO
RTO
```

and the technical correctness of the backup.

---

# 58. Restore Verification

Do not stop at:

```
VM boots successfully
```

Check:

```
Application starts
Database opens
Expected data exists
Users can authenticate
Services communicate
Files are readable
Configuration is correct
No unexpected corruption exists
```

A successful boot is not equivalent to successful application recovery.

---

# 59. Disaster Recovery

Disaster Recovery is the collection of processes and technologies used to restore IT services after disruptive events.

A disaster can include:

```
Complete server-room failure
Storage destruction
Ransomware
Large-scale configuration corruption
Data-center outage
Regional outage
Major human error
```

DR usually involves:

```
People
Processes
Technology
Documentation
Testing
```

It is therefore broader than simply installing a backup tool.

---

# 60. Disaster Recovery Site Types

A recovery site may be categorized conceptually as:

```
Cold Site
Warm Site
Hot Site
```

A cold site provides infrastructure but requires substantial preparation before workloads can operate.

A warm site has some infrastructure and data prepared.

A hot site is designed for rapid activation and may have continuously replicated workloads.

The faster the recovery requirement, the greater the cost and complexity tend to be.

---

# 61. Active-Passive Architecture

A common HA/DR model is:

```
Primary Site
     │
     │ replication
     ▼
Secondary Site
```

The primary environment serves users.

The secondary environment remains ready to take over.

This is:

```
Active-Passive
```

During a disaster:

```
Primary ✕
   ↓
Secondary becomes active
```

---

# 62. Active-Active Architecture

In an active-active design, both environments serve traffic.

For example:

```
             Users
             /   \
            ▼     ▼
         Site A  Site B
           │       │
        Service Service
```

Traffic can be distributed across both sites.

If Site A fails:

```
Site B
  ↓
Continues serving users
```

Active-active designs can reduce failover time but are more complex.

Data consistency becomes particularly important.

---

# 63. Replication

Replication can operate at different layers:

```
Storage replication
VM replication
Database replication
Application replication
Container replication
```

The appropriate layer depends on the system.

For example:

```
Database replication
```

may be more appropriate than:

```
VM-level replication
```

when the database requires application-aware consistency and controlled recovery.

---

# 64. Synchronous Replication

Synchronous replication generally means that data is committed to multiple locations before the write is considered complete.

Conceptually:

```
Application
     ↓
Primary Storage
     │
     ├── Write
     │
     ▼
Secondary Storage
     │
     └── Acknowledge
```

This can provide very low data-loss exposure.

However, synchronous replication can introduce latency because the remote copy must participate in the write path.

It is therefore more suitable when the locations are sufficiently close and the infrastructure can support the latency requirements.

---

# 65. Asynchronous Replication

Asynchronous replication allows the primary system to continue without waiting for the remote copy to complete.

Conceptually:

```
Application
     ↓
Primary
     ↓
Replication Queue
     ↓
Secondary
```

Advantages:

```
Lower application latency
Longer geographic distance possible
```

Disadvantage:

```
Some recent data may be lost if the primary fails before replication completes
```

This directly affects RPO.

---

# 66. Failover Versus Disaster Recovery

These terms are often confused.

**Failover** usually refers to transferring service to another available component.

**Disaster recovery** refers to restoring service after a larger disruptive event.

For example:

```
Host A fails
   ↓
VM starts on Host B
```

This is a local HA failover.

But:

```
Entire data center destroyed
   ↓
Recover systems at secondary site
```

is disaster recovery.

---

# 67. Automatic Failover Is Not Always Appropriate

Automatic failover is useful when:

```
Failure is clearly detectable
Recovery target is healthy
State is safe to transfer
Fencing is reliable
The recovery action is predictable
```

Automatic failover can be dangerous when:

```
The failure is ambiguous
Data may be corrupted
Both sites may be affected
The recovery target is not trustworthy
Human approval is required
```

Some disaster scenarios require controlled manual activation.

---

# 68. Disaster Recovery Runbook

A DR runbook is a documented sequence of recovery actions.

A basic runbook may contain:

```
1. Declare incident
2. Identify affected systems
3. Determine failure scope
4. Protect remaining data
5. Confirm recovery site
6. Confirm backups / replication
7. Activate recovery infrastructure
8. Restore required data
9. Start core dependencies
10. Start application services
11. Verify functionality
12. Redirect users
13. Monitor
14. Document actions
15. Plan failback
```

The exact procedure depends on the organization.

---

# 69. Recovery Dependencies

Applications rarely exist in isolation.

Suppose:

```
Web Application
      ↓
API
      ↓
Database
      ↓
Storage
```

If you restore the Web application before the database, it may not function.

Therefore, DR planning should map dependencies:

```
Network
   ↓
Identity / DNS
   ↓
Storage
   ↓
Database
   ↓
Backend Services
   ↓
Frontend
```

The actual order depends on the system architecture.

---

# 70. DNS and Failover

DNS can be part of a DR design.

For example:

```
app.example.com
       ↓
Primary Site
```

After a disaster, DNS can be changed to:

```
app.example.com
       ↓
Secondary Site
```

However, DNS changes are not instantaneous in every environment because clients and resolvers cache DNS records according to TTL.

Therefore, DNS-based failover must consider:

```
TTL
Caching
Health checks
Client behavior
Application session state
```

---

# 71. Load Balancers and Failover

Load balancers can detect unhealthy backend servers.

For example:

```
Load Balancer
    │
    ├── Server A → Healthy
    └── Server B → Healthy
```

If Server A fails:

```
Load Balancer
    │
    └── Server B → Healthy
```

Traffic can be directed only to healthy backends.

This is a form of application-level failover.

It is different from restarting a failed VM, but the two mechanisms can complement one another.

---

# 72. Health Checks

A health check should answer a meaningful question.

A TCP check might answer:

```
Is the port accepting connections?
```

An HTTP check might answer:

```
Does the application return a valid response?
```

A deeper health check might answer:

```
Can the application reach its database?
Can it access required dependencies?
```

The deeper the check, the more accurately it may represent actual application health, but overly aggressive dependency checks can also cause healthy instances to be marked unhealthy.

Health checks should therefore be designed carefully.

---

# 73. HA and Backup Solve Different Failure Classes

Consider several scenarios.

### Host failure

```
HA failover
→ Very useful
```

### VM crash

```
HA / orchestration
→ May restart workload
```

### Accidental file deletion

```
Backup
→ Required
```

### Ransomware

```
Immutable/offline backup
→ Critical
```

### Entire data center destroyed

```
Off-site DR
→ Required
```

This illustrates why no single technology provides complete resilience.

---

# 74. Defense in Depth

A resilient architecture uses multiple layers.

For example:

```
Layer 1 → Redundant hardware
Layer 2 → HA cluster
Layer 3 → Application replicas
Layer 4 → Replicated storage
Layer 5 → Backups
Layer 6 → Off-site copies
Layer 7 → Restore testing
Layer 8 → Disaster recovery procedures
```

If one layer fails, another can provide protection.

This is called defense in depth.

---

# 75. Example Architecture

Consider a company with a Web application.

A resilient design could be:

```
                         Internet
                            │
                            ▼
                       Load Balancer
                       /           \
                      ▼             ▼
                   Node A         Node B
                    │               │
                 App Pod         App Pod
                    │               │
                    └──────┬────────┘
                           ▼
                      Database HA
                           │
                     Replicated Data
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
             Local Backup      Off-site Backup
```

This architecture uses multiple resilience mechanisms.

---

# 76. What Happens When a Node Fails?

Suppose Node A fails.

The sequence may be:

```
Node A fails
     ↓
Health detection
     ↓
Workload becomes unavailable
     ↓
Node B continues serving traffic
     ↓
Orchestrator starts replacement workload
     ↓
Service capacity restored
```

If the application has three replicas and only one node fails, the application may continue with reduced capacity.

---

# 77. What Happens When Storage Fails?

Storage failure is more serious.

If the application depends on a single storage system:

```
Storage fails
      ↓
VMs / application data unavailable
```

HA compute nodes alone cannot solve this.

A resilient design may require:

```
Storage redundancy
Replication
Snapshots
Backups
Off-site copies
```

This is why infrastructure resilience must be analyzed end-to-end.

---

# 78. What Happens When Data Is Corrupted?

Suppose:

```
Application
    ↓
Database
    ↓
Corrupted data
```

Replication may replicate the corruption.

HA may keep the corrupted database online.

The correct recovery mechanism may be:

```
Backup
   ↓
Restore to known-good point
```

This is one of the clearest examples of why HA and backup are complementary.

---

# 79. Backup and Recovery of a VM

A typical recovery process might be:

```
Select backup
     ↓
Choose restore location
     ↓
Restore VM configuration
     ↓
Restore virtual disks
     ↓
Connect network
     ↓
Boot VM
     ↓
Check OS
     ↓
Check application
     ↓
Check data
     ↓
Declare service recovered
```

The restoration process should be documented before a disaster occurs.

---

# 80. Practical Lab Environment

For learning, you can simulate HA and DR using virtual machines.

A possible laboratory architecture is:

```
Physical Computer
       │
       ├── VM 1 → Hypervisor Node A
       ├── VM 2 → Hypervisor Node B
       ├── VM 3 → Storage / Backup Server
       └── VM 4 → Test Client
```

You can use virtualization technologies such as KVM, Proxmox, or VirtualBox for the laboratory.

The exact implementation depends on the available hardware.

---

# 81. Practical Lab — Identify SPOFs

Create a simple architecture:

```
Client
  ↓
Switch
  ↓
Server A
  ↓
Storage
```

List all potential SPOFs.

You should identify at least:

```
Switch
Server A
Storage
```

Now redesign it:

```
Client
 /   \
SW1  SW2
 |    |
A    B
 \    /
 Storage HA
```

Explain which failures the new architecture can tolerate.

---

# 82. Practical Lab — Simulate Host Failure

Create two virtualized nodes.

Deploy a workload on Node A.

Document:

```
VM name
IP address
Storage location
Network
Application
```

Then simulate a Node A failure.

In a real HA platform, the cluster should detect the failure and recover the workload on Node B.

If your laboratory does not support automatic HA, perform the recovery manually and document every step.

The learning objective is to understand the recovery sequence rather than simply clicking a failover button.

---

# 83. Practical Lab — Observe Failure Detection

If your cluster supports health monitoring, observe the normal state:

```
Node A → Healthy
Node B → Healthy
```

Then disconnect or stop the appropriate test node.

Observe:

```
Heartbeat loss
Cluster detection
Node state change
Failover decision
Workload recovery
```

Record the approximate duration.

This gives you an empirical understanding of failover time.

---

# 84. Practical Lab — Backup a VM

Create a test VM.

Before the backup, record:

```
Hostname
IP configuration
Installed application
Test file
Application configuration
```

Create a backup.

Then verify:

```
Backup job succeeded
Backup exists
Backup metadata is correct
Backup repository has sufficient capacity
```

Do not stop here.

The next exercise is more important.

---

# 85. Practical Lab — Restore a VM

Restore the test VM into an isolated network.

Verify:

```
VM boots
Filesystem is intact
Application starts
Test file exists
Network configuration is correct
Application responds
```

Record:

```
Backup time
Restore start
Restore completion
Application availability
```

Calculate your approximate RTO.

---

# 86. Practical Lab — Test File Recovery

Inside a test VM, create:

```
important.txt
```

Put a known value inside.

Back up the system.

Delete the file.

Then restore the file from backup.

Verify:

```
File exists
Contents are correct
```

This demonstrates a simple but important concept:

> Restoration should be tested at the level of the data users actually care about.

---

# 87. Practical Lab — Backup Failure Simulation

Create a backup job and intentionally introduce a failure condition in a laboratory environment, such as:

```
Insufficient destination space
Unavailable repository
Incorrect path
Permission failure
```

Observe the backup system.

Then answer:

```
How was failure reported?
Who would receive the alert?
Would an administrator notice immediately?
How would you verify whether recoverable data exists?
```

This teaches operational monitoring rather than merely backup configuration.

---

# 88. Practical Lab — RPO Exercise

Assume:

```
Full backup:
Sunday 00:00

Incremental backups:
Every 6 hours
```

A failure occurs:

```
Wednesday 14:00
```

Determine the maximum theoretical amount of data that could be lost under the backup schedule.

Then redesign the schedule for:

```
RPO ≤ 1 hour
```

Explain the trade-off in:

```
Storage
Network traffic
Backup processing
Operational complexity
```

---

# 89. Practical Lab — RTO Exercise

Assume:

```
Backup size = 500 GB
Restore throughput = 200 MB/s
```

Estimate the theoretical transfer time.

Then add realistic delays for:

```
VM creation
Storage initialization
OS boot
Application startup
Database recovery
Testing
DNS / traffic redirection
```

Compare the result with:

```
RTO = 30 minutes
```

If the recovery process cannot meet the RTO, propose improvements.

---

# 90. Practical Lab — 3-2-1 Backup Design

Design a backup architecture for a small company.

The company has:

```
2 virtualization hosts
10 VMs
1 TB total VM data
```

Design:

```
3 copies
2 storage systems/media
1 off-site copy
```

Your architecture should specify:

```
Production location
Local backup repository
Off-site destination
Backup frequency
Retention
Encryption
Restore testing
```

---

# 91. Practical Lab — Kubernetes Resilience

Use the Kubernetes cluster from Chapter 5.

Create:

```
Deployment
replicas = 3
```

Spread the Pods across available nodes if your laboratory has multiple workers.

Then simulate a worker-node failure.

Observe:

```
kubectl get nodes
kubectl get pods -o wide
```

Watch the Deployment recover.

Explain:

```
Which Pods disappeared?
What did the ReplicaSet do?
Where was the replacement scheduled?
How did the Service continue providing access?
```

---

# 92. Practical Lab — Kubernetes Persistent Data

Create a test application that writes data to a Persistent Volume.

Perform:

```
Write data
Delete application Pod
Recreate Pod
Verify data
```

Then ask:

```
Where did the data live?
Was it inside the container?
Was it inside the Pod filesystem?
Was it in persistent storage?
```

This exercise reinforces the difference between ephemeral workloads and persistent state.

---

# 93. Practical Lab — Backup and Restore a Kubernetes Application

For a simple application, identify:

```
Deployment YAML
Service YAML
ConfigMap
Secret
Persistent data
Container image
Database
```

Create a recovery package containing the necessary definitions and data backups.

Then simulate:

```
Application removed
Persistent data removed
```

Recover the application in a separate namespace or test cluster.

Verify:

```
Pods running
Service available
Configuration correct
Data restored
Application functional
```

This is much closer to real DR work than simply saving YAML files.

---

# 94. Troubleshooting: HA Cluster Does Not Fail Over

Possible causes include:

```
Node failure not detected
Heartbeat network failure
Quorum unavailable
Fencing misconfigured
Storage unavailable
Workload not marked for HA
Target node lacks capacity
Network configuration incorrect
Cluster service failure
```

A disciplined troubleshooting sequence is:

```
Check cluster state
      ↓
Check node membership
      ↓
Check quorum
      ↓
Check fencing
      ↓
Check storage
      ↓
Check workload state
      ↓
Check target node capacity
      ↓
Check networking
```

Do not immediately force-start the workload on another node without understanding the state of the original node.

---

# 95. Troubleshooting: VM Will Not Start After Failover

Possible causes include:

```
VM disk inaccessible
Storage unavailable
Corrupted virtual disk
Insufficient CPU
Insufficient RAM
Network bridge missing
Storage path missing
Lock remains active
VM configuration invalid
```

Check:

```
Storage
Networking
Cluster state
VM configuration
Resource availability
```

The important question is:

> Did the compute node fail, or did one of its dependencies fail as well?

---

# 96. Troubleshooting: Backup Job Succeeds but Restore Fails

This is a critical scenario.

Possible causes include:

```
Corrupt backup
Incomplete backup
Missing dependent data
Incompatible restore environment
Encryption key unavailable
Backup metadata corruption
Application inconsistency
Incorrect restore procedure
```

This is why restore tests are essential.

A successful backup job is not proof of successful recovery.

---

# 97. Troubleshooting: Backup Repository Is Full

If the backup destination reaches capacity:

```
New backups may fail
Retention may not work
Replication may stop
Recovery points may disappear
```

Investigate:

```
Retention policy
Backup growth
Deduplication
Compression
Repository capacity
Old backups
Unexpected workloads
```

Then determine whether the existing retention policy still meets RPO and compliance requirements.

---

# 98. Troubleshooting: Replication Is Behind

Suppose:

```
Primary → Secondary
```

but replication lag grows.

Possible causes include:

```
Network bandwidth
Storage latency
High write workload
Replication process failure
Destination capacity
CPU saturation
Network packet loss
```

Replication lag directly affects recovery point.

If:

```
Replication lag = 45 minutes
```

then the practical RPO may be at least approximately 45 minutes under the relevant failure scenario.

---

# 99. Troubleshooting: Failover Happened Too Quickly

An HA system can experience false failure detection.

For example:

```
Temporary network interruption
       ↓
Heartbeat lost
       ↓
Cluster assumes node failure
       ↓
Failover
```

If the original node is still operating, split-brain risk exists.

This is why HA systems need:

```
Quorum
Fencing
Reliable health detection
Appropriate timeouts
Redundant communication paths
```

HA configuration should prioritize correctness over aggressively short failover timers.

---

# 100. Backup Security

Backup infrastructure is part of the organization's security perimeter.

Protect it with:

```
Strong authentication
MFA
Least privilege
Network segmentation
Encryption
Immutable retention
Separate credentials
Monitoring
Audit logs
```

A compromised backup system can turn a recoverable incident into a catastrophic one.

---

# 101. Recovery Testing Frequency

Recovery testing should be performed regularly.

Testing frequency depends on:

```
Business criticality
Regulatory requirements
Change frequency
Infrastructure complexity
Risk tolerance
```

Possible tests include:

```
Individual file restore
VM restore
Database restore
Application restore
Host failover
Site failover
Full disaster simulation
```

Testing should gradually increase in scope.

---

# 102. Documentation

A recovery plan should not exist only in one administrator's memory.

Document:

```
System inventory
Dependencies
Backup locations
Credentials process
Recovery procedures
Network information
DNS information
Storage configuration
Failover process
Rollback process
Validation steps
Escalation contacts
```

Documentation should be reviewed after significant infrastructure changes.

---

# 103. The Difference Between HA, Backup, and DR

This distinction should become automatic in your thinking.

### High Availability

```
Goal:
Keep service running during component failure.
```

### Backup

```
Goal:
Preserve recoverable historical data.
```

### Disaster Recovery

```
Goal:
Restore services after major disruption.
```

A resilient organization generally needs all three.

---

# 104. A Practical Resilience Matrix

Consider the following failures:

|Failure|HA|Backup|DR|
|---|---|---|---|
|VM crash|Very useful|Useful for data recovery|Usually unnecessary|
|Host failure|Very useful|Useful|Usually unnecessary|
|Single disk failure|Depends on storage redundancy|Useful|Usually unnecessary|
|Accidental file deletion|Not sufficient|Essential|Usually unnecessary|
|Database corruption|Not sufficient|Essential|Potentially useful|
|Ransomware|Not sufficient|Essential, ideally immutable|Potentially essential|
|Data-center destruction|Not sufficient|Off-site copy required|Essential|
|Regional disaster|Not sufficient|Off-site/geographically separated copy|Essential|

The important conclusion is that different technologies protect against different failure classes.

---

# 105. Designing an HA and DR Strategy

A systematic process is:

```
1. Identify services
2. Identify dependencies
3. Identify failure scenarios
4. Identify SPOFs
5. Define RPO
6. Define RTO
7. Design redundancy
8. Design failover
9. Design backup
10. Design off-site recovery
11. Document procedures
12. Test recovery
13. Measure results
14. Improve the design
```

This is more reliable than simply purchasing an HA or backup product.

---

# 106. Business Impact Analysis

Before selecting technical solutions, determine which services matter most.

For each service, identify:

```
Business importance
Maximum downtime
Acceptable data loss
Dependencies
Required recovery order
```

For example:

```
Email:
RTO = 4 hours
RPO = 1 hour

Payment system:
RTO = 15 minutes
RPO = 5 minutes

Internal test environment:
RTO = 2 days
RPO = 24 hours
```

Different services justify different levels of protection.

---

# 107. Cost Versus Availability

Higher availability usually costs more.

For example:

```
Single server
→ Low cost
→ High risk

Two servers
→ Higher cost
→ Better availability

Cluster + redundant storage
→ Higher cost
→ Better failure tolerance

Multi-site active-active
→ Very high cost
→ Strong resilience
```

The objective is not to maximize redundancy without limits.

The objective is to meet the business requirement at an appropriate cost.

---

# 108. HA Does Not Mean Zero Downtime

Even a well-designed HA system can have:

```
Failure detection time
Failover time
Application startup time
DNS propagation
Connection interruption
Session loss
Data recovery
```

Therefore, HA normally means:

> Minimize service interruption.

It does not automatically mean:

> No user experiences any interruption whatsoever.

Some specialized fault-tolerant systems can approach continuous operation, but they involve different architectures and costs.

---

# 109. Graceful Degradation

A highly resilient application may continue operating in a reduced mode.

For example:

```
Normal:
Search + Orders + Recommendations

Failure:
Search + Orders
Recommendations unavailable
```

The application remains usable even though one feature is down.

This is called graceful degradation.

It can be more practical than requiring every dependency to remain available at all times.

---

# 110. Dependency Redundancy

Suppose:

```
Application A
     ↓
Database
```

Even if Application A has five replicas, a single database remains a bottleneck and potential SPOF.

A more resilient design might include:

```
Application
     ↓
Database HA
     ↓
Replicated Storage
     ↓
Backups
```

This demonstrates an important principle:

> Redundancy must exist across the entire dependency chain.

---

# 111. Recovery Order

Suppose an application depends on:

```
DNS
Identity
Database
API
Frontend
```

A sensible recovery sequence may be:

```
1. Infrastructure
2. Network
3. Storage
4. Identity / DNS
5. Database
6. Backend/API
7. Frontend
8. Monitoring
```

The exact order depends on the architecture.

The point is to identify dependencies before a disaster.

---

# 112. Recovery Validation

After recovery, do not simply declare success because the servers are online.

Perform validation:

```
Infrastructure healthy
        ↓
Network healthy
        ↓
Storage healthy
        ↓
Database healthy
        ↓
Application healthy
        ↓
User transaction succeeds
```

A real transaction test is often more meaningful than checking whether a process is running.

---

# 113. Failover Testing

A failover mechanism that has never been tested is an assumption.

Testing should answer:

```
Does failure detection work?
Does quorum work?
Does fencing work?
Does workload restart?
Is storage accessible?
Is networking correct?
Does the application recover?
How long does recovery take?
```

The test results should be documented.

---

# 114. Planned Versus Unplanned Failover

A **planned failover** occurs when administrators intentionally move workloads.

Examples:

```
Maintenance
Hardware replacement
Software upgrade
Data-center maintenance
```

An **unplanned failover** occurs because a failure has already happened.

Planned failover is useful for testing:

```
Can we move workloads safely?
```

Regular planned failover exercises can increase confidence in the recovery process.

---

# 115. Maintenance Without Downtime

HA can also support planned maintenance.

Suppose:

```
Host A → workloads
Host B → spare capacity
```

You can migrate workloads away from Host A:

```
Host A → empty
Host B → workloads
```

Then perform maintenance.

This is different from failure recovery because the original host is intentionally taken offline.

Virtualization platforms can support live migration in appropriate configurations.

---

# 116. Live Migration Versus Failover

These concepts are different.

**Live migration** moves a running VM from one host to another with little interruption.

```
Host A
   ↓
Running VM
   ↓
Live Migration
   ↓
Host B
```

**Failover** typically occurs after a failure:

```
Host A ✕
   ↓
Recover VM on Host B
```

Live migration is proactive.

Failover is reactive.

---

# 117. Maintenance Strategy

A resilient infrastructure should use planned maintenance to reduce unexpected failures.

For example:

```
1. Check cluster health
2. Verify backup
3. Migrate workloads
4. Put node into maintenance
5. Perform maintenance
6. Validate node
7. Return node to cluster
8. Restore workload distribution
```

The exact process depends on the platform.

---

# 118. Backup Before Major Changes

Before major infrastructure changes, consider creating a verified recovery point.

For example:

```
Before hypervisor upgrade:
→ Verify backups
→ Create recovery point
→ Confirm restore procedure
```

This provides protection against:

```
Upgrade failure
Configuration corruption
Unexpected compatibility problems
```

A backup is most valuable when it is created before the event that may destroy the system.

---

# 119. Change Management and Recovery

Production changes should include:

```
Change description
Risk assessment
Backup verification
Implementation plan
Validation plan
Rollback plan
Maintenance window
Responsible administrator
```

The rollback plan should answer:

```
What if the change fails?
What state should we restore?
How quickly can we restore it?
What dependencies changed?
```

This is operational resilience.

---

# 120. Example: Small Business HA/DR Architecture

Consider:

```
2 Virtualization Hosts
1 Shared / Distributed Storage System
1 Backup Server
1 Off-site Backup Destination
```

The design could be:

```
                  Users
                    │
                    ▼
               Network
                    │
           ┌────────┴────────┐
           ▼                 ▼
        Host A             Host B
           │                 │
           └────────┬────────┘
                    ▼
              VM Workloads
                    │
                    ▼
              Storage System
                    │
                    ▼
              Backup Server
                    │
                    ▼
           Off-site Repository
```

Host failure is handled by HA.

Data corruption is handled by backup.

Site failure is handled by the off-site copy.

---

# 121. Example: Kubernetes HA/DR Architecture

A Kubernetes-oriented design might be:

```
                    Users
                      │
                      ▼
                 Load Balancer
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        Node 1      Node 2      Node 3
          │           │           │
        Pods        Pods        Pods
          └───────────┼───────────┘
                      ▼
                 Persistent Data
                      │
              ┌───────┴────────┐
              ▼                ▼
        Local Backup       Off-site Backup
```

The application is resilient because:

```
Pods are replicated
Nodes are redundant
Service provides stable access
Persistent data is protected
Backups provide historical recovery
```

---

# 122. Common Beginner Misconceptions

### "RAID is a backup."

No.

RAID can provide disk fault tolerance, but it does not protect against accidental deletion, ransomware, or major system destruction.

### "Replication is a backup."

No.

Replication can replicate corruption or deletion.

### "A snapshot is a backup."

Not necessarily.

A snapshot may remain inside the same failure domain.

### "HA means the system can never go down."

No.

HA reduces service interruption during certain failures.

### "Three servers automatically provide HA."

No.

They need appropriate clustering, networking, storage, quorum, and workload configuration.

### "If the backup job says successful, recovery is guaranteed."

No.

You must test restoration.

### "Failover is always better when it happens immediately."

No.

Incorrect or premature failover can cause split brain or data corruption.

---

# 123. The Most Important HA Concepts

You should be able to explain these terms without notes:

```
SPOF
Redundancy
Failover
Failback
Heartbeat
Quorum
Fencing
Split brain
Failure domain
RPO
RTO
MTBF
MTTR
```

These terms form the vocabulary required to discuss resilient infrastructure professionally.

---

# 124. The Most Important Backup Concepts

You should also understand:

```
Full backup
Incremental backup
Differential backup
Retention
Backup repository
3-2-1 strategy
Immutable backup
Off-site backup
Application consistency
Snapshot
Restore test
```

The important skill is not memorizing definitions individually.

You should understand how they combine into a recovery strategy.

---

# 125. Complete HA Design Exercise

Design an infrastructure for:

```
A company with:
2 virtualization hosts
20 virtual machines
1 critical database
1 Web application
1 internal application
50 users
```

Requirements:

```
Host failure must not cause a long outage.
Critical database must have strong protection.
Backups must survive a site-level incident.
```

Design:

```
Compute redundancy
Storage redundancy
Network redundancy
VM failover
Database protection
Backup schedule
Off-site protection
RPO
RTO
Restore testing
```

Then identify every remaining SPOF.

---

# 126. Complete DR Design Exercise

Assume the primary data center is completely unavailable.

You have:

```
Off-site backups
Secondary virtualization environment
DNS control
Application documentation
```

Create a recovery runbook.

Your runbook should specify:

```
Who declares the disaster?
What is recovered first?
Where are backups?
How are VMs restored?
How are databases restored?
How is networking configured?
How is DNS redirected?
How is application functionality verified?
How is the RTO measured?
How is the original site recovered?
```

This is the type of thinking expected from an infrastructure administrator.

---

# 127. Complete Failure Scenario Exercise

Consider:

```
Host A
   ↓
VM Database
   ↓
Application
```

Host A suddenly fails.

At the same time, the network between Host A and Host B is unstable.

Answer:

```
Should Host B immediately start the VM?
What could happen if Host A is still running?
Why is fencing important?
How does quorum help?
What happens if storage is also unavailable?
What backup strategy protects against database corruption?
```

This scenario combines several concepts from the chapter.

---

# 128. Complete Recovery Exercise

Imagine:

```
Ransomware encrypts production VMs.
```

The attacker also attempts to access the backup repository.

Design the recovery strategy using:

```
Immutable backups
Off-site backups
Separate credentials
Backup retention
Restore testing
Clean recovery environment
Application validation
```

Then explain why simply having a second copy on the same network is insufficient.

---

# 129. Practical Recovery Timeline

A recovery timeline may look like:

```
00:00 → Failure detected
00:05 → Incident declared
00:10 → Failure scope confirmed
00:15 → Recovery decision
00:20 → Recovery environment activated
00:30 → Storage available
00:40 → Database restored
00:50 → Application restored
01:00 → Functional validation
01:10 → Users redirected
```

If:

```
RTO = 60 minutes
```

this recovery process would be right at the target and leave little margin.

Therefore, recovery processes should ideally complete comfortably inside the required RTO.

---

# 130. Measuring Recovery

After a recovery exercise, record:

```
Detection time
Decision time
Failover time
Restore time
Validation time
Total downtime
Data loss
```

Then compare:

```
Actual RTO vs Required RTO
Actual RPO vs Required RPO
```

For example:

```
Required RTO = 60 min
Actual RTO = 85 min
```

The system does not meet the requirement.

That is useful information because it tells you where engineering work is required.

---

# 131. Improving RTO

If recovery takes too long, possible improvements include:

```
Automation
Pre-provisioned recovery infrastructure
Faster storage
More bandwidth
More frequent testing
Application replication
Warm standby
Hot standby
Faster database recovery
Simplified recovery procedures
```

The correct improvement depends on where the recovery bottleneck exists.

---

# 132. Improving RPO

If too much data is lost, possible improvements include:

```
More frequent backups
Transaction-log backups
Continuous replication
Synchronous replication
Asynchronous replication with lower lag
Application-level replication
```

Again, the correct solution depends on the workload.

---

# 133. Availability Is an End-to-End Property

A service is available only if its entire dependency chain works.

For example:

```
User
 ↓
DNS
 ↓
Network
 ↓
Load Balancer
 ↓
Application
 ↓
Database
 ↓
Storage
```

If any critical dependency fails:

```
Service unavailable
```

Therefore, measuring only server uptime is insufficient.

A server can have:

```
99.99% uptime
```

while the application has much lower availability due to dependency failures.

---

# 134. Final Synthesis

High availability and disaster recovery are complementary disciplines.

High availability minimizes service interruption when components fail.

Automatic failover allows workloads to move or restart on healthy infrastructure.

Quorum helps a cluster make safe decisions.

Fencing protects against split brain and simultaneous ownership.

Redundant compute is not enough if storage, networking, or power remains a single point of failure.

Backups provide historical recovery points.

Replication can reduce recovery time and data loss, but it does not replace backups.

RPO defines acceptable data loss.

RTO defines acceptable recovery time.

The 3-2-1 rule provides a strong foundation for backup architecture.

Restore testing verifies that backups are actually usable.

Disaster recovery extends these concepts to major incidents such as site destruction, ransomware, or large-scale infrastructure failure.

The complete mental model is:

```
                 RESILIENCE
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
 High Availability          Disaster Recovery
        │                         │
        ▼                         ▼
 Redundancy                  Backups
 Failover                   Replication
 Quorum                     Off-site Copies
 Fencing                    Restore
 Health Checks              DR Runbooks
        │                         │
        └────────────┬────────────┘
                     ▼
                Recovery
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
        RPO                    RTO
  Acceptable data loss   Acceptable downtime
```

The most important principle to retain is:

> **High availability keeps services running through expected component failures; backups and disaster recovery allow you to recover when failures exceed what HA can protect against.**

---

# 135. Final Knowledge Check

Before moving to the next chapter, answer these questions without consulting the lesson.

## Availability Fundamentals

1. What does high availability mean?
    
2. What is the difference between availability and reliability?
    
3. What is a Single Point of Failure?
    
4. Give five examples of possible infrastructure SPOFs.
    
5. What is redundancy?
    
6. Why can redundancy fail to provide protection if components share the same failure domain?
    
7. What is failover?
    
8. What is failback?
    
9. What is automatic failover?
    
10. Why is failure detection important?
    

## Cluster Concepts

11. What is a heartbeat?
    
12. Why can a missing heartbeat be ambiguous?
    
13. What is split brain?
    
14. Why is split brain dangerous?
    
15. What is quorum?
    
16. Why can two-node clusters be difficult to design safely?
    
17. What is fencing?
    
18. Why is fencing important before restarting a workload elsewhere?
    
19. What is a failure domain?
    
20. Give three examples of failure domains.
    

## Virtualization HA

21. How can virtualization help with server failover?
    
22. Why can a VM be restarted on another host?
    
23. What role can shared storage play in VM HA?
    
24. Why can shared storage itself become a SPOF?
    
25. What is distributed storage?
    
26. What is the difference between VM failover and live migration?
    
27. What is the difference between Proxmox HA and KVM itself?
    
28. Why is VirtualBox useful for learning but generally not the first choice for production HA?
    

## Kubernetes HA

29. How does Kubernetes respond when a Pod disappears?
    
30. How does Kubernetes respond when a worker node fails?
    
31. Why is running three replicas on one node not equivalent to distributing them across three nodes?
    
32. What role does a Service play in application availability?
    
33. Why should persistent data be treated differently from ephemeral Pods?
    

## Backup

34. What is a backup?
    
35. What is a full backup?
    
36. What is an incremental backup?
    
37. What is a differential backup?
    
38. What is the difference between a snapshot and a backup?
    
39. Why is RAID not a backup?
    
40. Why is replication not a replacement for backup?
    
41. What is the 3-2-1 backup rule?
    
42. Why should at least one backup copy be off-site?
    
43. Why are immutable backups useful against ransomware?
    
44. Why should backup repositories be protected with separate access controls?
    
45. What is backup retention?
    

## Recovery Objectives

46. What does RPO mean?
    
47. What does RTO mean?
    
48. What is the difference between RPO and RTO?
    
49. If backups run every 24 hours, can the organization automatically claim an RPO of 1 hour?
    
50. If a restore takes 4 hours, can the organization claim an RTO of 30 minutes?
    
51. What is MTTR?
    
52. What is MTBF?
    
53. How does reducing MTTR affect availability?
    

## Disaster Recovery

54. What is disaster recovery?
    
55. What is the difference between HA and DR?
    
56. What is an active-passive DR architecture?
    
57. What is an active-active architecture?
    
58. What is the difference between synchronous and asynchronous replication?
    
59. How does asynchronous replication affect RPO?
    
60. Why can DNS be part of a DR strategy?
    
61. Why can a DR plan fail if application dependencies are not documented?
    
62. What should a DR runbook contain?
    
63. Why must disaster recovery be tested?
    
64. What is the difference between a planned and unplanned failover?
    

## Practical Reasoning

65. A server fails, but another server can immediately start the VM. Which HA mechanism is being used?
    
66. A file is accidentally deleted and must be recovered from yesterday. Is this primarily an HA or backup problem?
    
67. An entire data center is destroyed. Is local HA sufficient?
    
68. A database is corrupted and the corruption is replicated to a secondary system. Why might replication not help?
    
69. A backup job reports success but the restore fails. What does this teach you?
    
70. A cluster automatically fails over a workload while the original node is still active. What dangerous condition might exist?
    
71. Why should failover testing include storage and networking, not only compute nodes?
    
72. If the required RTO is 15 minutes but recovery takes 45 minutes, what should the organization do?
    
73. If the required RPO is 5 minutes but replication lag is 30 minutes, does the architecture meet the requirement?
    
74. Why is a recovery environment that has never been tested a risk?
    
75. Explain the complete relationship between:
    

```
HA
Failover
Backup
Replication
RPO
RTO
DR
Restore testing
```

---

# 136. Final Mental Model

If you remember only one model from this chapter, remember:

```
                    FAILURE
                       │
          ┌────────────┴────────────┐
          │                         │
      Recoverable               Major Incident
      Component Failure               │
          │                           │
          ▼                           ▼
      High Availability          Disaster Recovery
          │                           │
          ▼                           ▼
       Detection                  Backup / Replica
          │                           │
          ▼                           ▼
       Quorum                     Recovery Site
          │                           │
          ▼                           ▼
       Fencing                    Restore Data
          │                           │
          ▼                           ▼
       Failover                  Start Services
          │                           │
          └────────────┬──────────────┘
                       ▼
                 Service Restored
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
            RTO                 RPO
       How long to recover?  How much data lost?
```

And remember the deepest operational principle:

> **Do not ask only whether a system can survive a failure. Ask which failures it can survive, how quickly it recovers, how much data can be lost, what happens when redundancy itself fails, and whether the recovery procedure has actually been tested.**

That is the foundation of professional high-availability and disaster-recovery engineering.