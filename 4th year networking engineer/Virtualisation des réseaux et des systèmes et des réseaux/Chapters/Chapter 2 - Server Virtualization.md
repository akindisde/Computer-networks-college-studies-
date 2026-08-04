# Chapter 2 — Server Virtualization

## Introduction

Server virtualization is one of the most important practical applications of virtualization technology. In the previous chapter, we introduced virtualization as a way of abstracting physical computing resources and presenting them as logical resources. A virtual machine can have virtual CPUs, virtual memory, virtual disks, and virtual network interfaces even though the underlying physical resources remain on a real server.

This chapter moves from the general idea of virtualization to the practical administration of virtual servers. Creating a VM is only the beginning. An administrator must decide how much CPU and memory it should receive, how its storage should be organized, how its network interfaces should be connected, how its configuration should be maintained, and how its data should be protected.

A useful way to think about a virtual server is as a complete server environment whose hardware has been abstracted by a virtualization platform:

```
                Physical Server
        ┌────────────────────────────┐
        │ CPU / RAM / Storage / NIC  │
        └──────────────┬─────────────┘
                       │
                       ▼
                 Hypervisor
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
      VM 1           VM 2           VM 3
      Server         Server         Server
        │              │              │
       OS             OS             OS
```

The administrator therefore works at several layers. The physical host must have sufficient capacity. The hypervisor must be configured correctly. Each VM must receive appropriate virtual resources. The guest operating system must be healthy. Finally, the workload must be protected against failures.

This chapter focuses on four major areas: the introduction to server virtualization, configuration of virtual machine parameters, management of CPU, RAM, and storage, and backup and restoration of virtual machines.

---

# 1. Learning Objectives

By the end of this chapter, you should be able to explain what server virtualization is and why organizations use virtual servers instead of dedicating a physical server to every workload.

You should understand the main parameters that define a virtual machine and be able to explain them from both the hypervisor's perspective and the guest operating system's perspective.

You should be able to reason about CPU allocation, memory allocation, virtual disk configuration, storage capacity, and resource contention rather than simply choosing large values.

You should also understand the difference between a VM snapshot and a backup, know why backups are necessary even when snapshots exist, and understand the basic workflow of backing up and restoring a virtual machine.

Finally, you should be able to think of virtual server administration as a lifecycle: plan, deploy, configure, monitor, protect, recover, and eventually retire.

---

# 2. Introduction to Server Virtualization

In a traditional physical server environment, a workload is closely associated with a physical machine. An organization might deploy one server for a Web application, another for DNS, another for a database, and another for monitoring.

```
Physical Server 1 → Web Server
Physical Server 2 → DNS Server
Physical Server 3 → Database Server
Physical Server 4 → Monitoring Server
```

This architecture is easy to understand, but it can be inefficient. A physical server may have many CPU cores and a large amount of RAM while its workload uses only a small percentage of those resources.

Server virtualization changes this relationship. Instead of assigning one physical server to each workload, a virtualization platform can host multiple virtual servers on a shared physical host.

```
                    Physical Server
                           │
                       Hypervisor
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
     Web VM             DNS VM          Database VM
        │                  │                  │
    Guest OS            Guest OS            Guest OS
```

The important concept is **resource sharing**. The hypervisor manages access to physical resources and presents virtual hardware to each guest.

Virtualization does not create additional physical CPU cycles or physical RAM. Instead, it allows available resources to be shared and scheduled between independent workloads.

This leads to server consolidation. Several workloads that previously required several physical servers may be hosted by fewer physical machines.

However, consolidation also introduces a dependency. If several VMs share one physical host, failure of that host can affect all of them. Therefore, virtualization increases the importance of monitoring, redundancy, backup, and recovery planning.

---

# 3. Why Organizations Virtualize Servers

## 3.1 Server Consolidation

Suppose an organization has ten physical servers and each uses only 10–20% of its CPU capacity most of the time. Maintaining ten machines may be unnecessary.

Virtualization allows the organization to consolidate workloads:

```
Before:

Server A → Application A
Server B → Application B
Server C → Application C
Server D → Application D


After:

                 Physical Host
                      │
                  Hypervisor
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
      VM A           VM B          VM C
```

Consolidation can reduce hardware requirements, power consumption, cooling requirements, and physical space.

But consolidation also concentrates workloads. A failed host can affect many services simultaneously. A professional virtualization design therefore combines consolidation with mechanisms such as redundant hosts, backups, replication, clustering, and disaster recovery.

---

## 3.2 Isolation

Virtual machines can separate workloads from one another.

Imagine that a production database is running correctly and an administrator wants to test a new database version. Installing the test software directly on production introduces risk.

Instead:

```
Production Database VM
          │
          │ isolated
          ▼
Test Database VM
```

The test VM can have its own operating system, packages, filesystem, network configuration, and applications.

This makes virtualization valuable for development, testing, cybersecurity laboratories, training, and legacy applications.

Isolation is not an absolute security guarantee. The hypervisor, management interface, storage, and virtual network remain important security boundaries.

---

## 3.3 Flexible Resource Allocation

A physical server has a fixed hardware configuration. A virtual server can often be resized.

A VM might begin with:

```
2 vCPU
4 GB RAM
50 GB disk
```

and later be changed to:

```
4 vCPU
8 GB RAM
100 GB disk
```

depending on platform capabilities and workload requirements.

This flexibility is valuable, but it must not lead to arbitrary over-allocation. The physical host still has finite resources.

---

# 4. A Virtual Machine as a Server

A VM can be understood as a collection of virtual hardware components and a guest operating system:

```
Virtual Machine
│
├── vCPU
├── Virtual RAM
├── Virtual Disk
├── vNIC
├── Virtual Firmware
└── Guest Operating System
```

From inside the guest, these resources can look very similar to physical hardware.

For example, on Linux:

```
lscpu
free -h
lsblk
ip addr
ip route
```

`lscpu` reports CPU information visible to the guest. `free -h` reports memory visible to the guest. `lsblk` displays block devices, while `ip addr` and `ip route` expose virtual networking.

The guest normally does not need to know how these resources are implemented physically.

A 100 GB virtual disk might be stored as a file on an SSD. A vCPU may be scheduled on a physical CPU core. A vNIC may connect to a software bridge and then to a physical network adapter.

The virtualization layer hides these implementation details behind an abstraction.

---

# 5. Configuration of Virtual Machine Parameters

Creating a VM is essentially the process of defining a virtual hardware specification.

A typical server VM might be described as:

```
VM Name: web-prod-01
OS: Debian Linux
CPU: 2 vCPU
RAM: 4 GB
Disk: 50 GB
Network: Server VLAN
Firmware: UEFI
```

The exact interface varies between VirtualBox, Proxmox VE, VMware, Hyper-V, QEMU/KVM, and other platforms, but the concepts remain similar.

---

## 5.1 Naming

A VM name is an administrative identifier. In a small laboratory, names such as `VM1` may be sufficient. In a real environment, naming becomes important because administrators may manage hundreds of systems.

A convention such as:

```
web-prod-01
web-prod-02
db-prod-01
dns-prod-01
monitoring-prod-01
```

communicates the role, environment, and instance number.

Good naming also helps monitoring, automation, documentation, and troubleshooting.

---

## 5.2 Guest Operating System

The guest operating system determines what software and drivers will run inside the VM.

Common server operating systems include Debian, Ubuntu Server, Rocky Linux, AlmaLinux, Windows Server, and FreeBSD.

The chosen operating system may influence firmware, virtual device models, drivers, and installation requirements.

A Linux VM and a Windows VM can coexist on the same physical host because each VM normally contains its own guest operating system.

---

## 5.3 CPU Configuration

The administrator chooses how many virtual CPUs the guest receives.

For example:

```
Web VM      → 2 vCPU
Database VM → 8 vCPU
DNS VM      → 1 vCPU
```

A vCPU is not a physical CPU core. It represents virtual processor capacity that the hypervisor schedules onto physical CPU resources.

This is why the correct question is not:

> "How many vCPUs can I give the VM?"

but:

> "How much CPU capacity does the workload actually require?"

A single-threaded application may gain little from receiving eight vCPUs. A highly parallel workload may benefit substantially from additional vCPUs.

---

## 5.4 Memory Configuration

The administrator assigns RAM to the VM.

For example:

```
Web VM      → 4 GB
Database VM → 16 GB
DNS VM      → 2 GB
```

The guest sees this memory as its available physical memory.

However, the physical host must also reserve memory for the hypervisor and other host functions.

If a host has 32 GB of RAM and VMs are assigned exactly 32 GB in total, there may be no useful headroom for the host or workload bursts.

Resource allocation must therefore consider both configured capacity and actual usage.

---

## 5.5 Virtual Disk

A VM normally uses one or more virtual disks.

The guest may see:

```
/dev/sda
```

while the host may see something completely different:

```
Guest /dev/sda
      ↓
Virtual Disk
      ↓
VDI / VMDK / QCOW2 / RAW / other representation
      ↓
Datastore
      ↓
Physical SSD/HDD
```

This layered model is important when troubleshooting storage.

The guest sees a disk. The virtualization platform determines how that disk maps onto physical storage.

---

## 5.6 Network Interface

A server VM normally requires at least one vNIC.

Conceptually:

```
VM
│
└── vNIC
     │
     ▼
Virtual Bridge / vSwitch
     │
     ▼
Physical NIC
     │
     ▼
Physical Network
```

The vNIC can be connected to NAT, a bridge, an isolated network, or another virtual network depending on the platform.

In production, the virtual network may also be associated with VLANs and security policies.

---

# 6. Virtual Firmware and Boot Configuration

A VM can have virtual BIOS or UEFI firmware.

The firmware initializes virtual hardware and selects a boot device.

A simplified boot sequence is:

```
VM starts
   ↓
Virtual firmware
   ↓
Boot device
   ↓
Bootloader
   ↓
Guest operating system
```

During operating-system installation, the VM may boot from an ISO image. After installation, it normally boots from its virtual disk.

The administrator may configure boot order, firmware mode, Secure Boot where supported, and other virtual hardware options.

These settings matter because a VM is not simply an application process. From the guest's perspective, it is presented as a machine that can perform a boot sequence.

---

# 7. Virtual Hardware and Device Models

A virtualization platform may provide several models of virtual hardware.

One approach is **device emulation**. The hypervisor presents a device that behaves like familiar physical hardware.

Another approach is **paravirtualization**, in which the guest uses drivers specifically designed to communicate efficiently with the virtualization layer.

In QEMU/KVM environments, **virtio** devices are an important example. Virtio can provide efficient virtualized network and storage devices without requiring the hypervisor to emulate a complete physical device.

The choice of virtual hardware can affect compatibility and performance.

---

# 8. CPU Resource Management

CPU management requires understanding the relationship between vCPUs and physical CPUs.

Suppose a physical host has eight physical CPU cores:

```
Host → 8 physical cores
```

The administrator creates four VMs:

```
VM1 → 4 vCPU
VM2 → 4 vCPU
VM3 → 4 vCPU
VM4 → 4 vCPU
```

The total configured vCPU allocation is 16, even though the host has eight physical cores.

This is **CPU overcommitment**.

Overcommitment is not automatically wrong. It assumes that workloads are not all consuming maximum CPU simultaneously.

If VM1 is busy while VM2, VM3, and VM4 are mostly idle, the host can schedule physical CPU time efficiently.

If all four become CPU-intensive simultaneously, however, they compete for the same physical resources.

---

# 9. CPU Contention

When multiple VMs need CPU capacity at the same time, the hypervisor must schedule their vCPUs onto physical CPU resources.

Suppose a host has eight physical cores and the running VMs collectively demand more than eight cores of actual processing capacity.

The hypervisor cannot provide unlimited physical CPU.

The result is contention.

The VMs may remain functional but experience increased latency or reduced performance.

This leads to an essential rule:

> **A vCPU allocation is not a permanent guarantee of a dedicated physical core.**

The actual behavior depends on the hypervisor, scheduling policy, physical CPU capacity, workload characteristics, and resource controls.

---

# 10. CPU Overcommitment

CPU overcommitment can be useful when workloads are bursty.

For example:

```
Physical host → 8 cores

VM1 → 4 vCPU
VM2 → 4 vCPU
VM3 → 2 vCPU
VM4 → 2 vCPU
VM5 → 2 vCPU
VM6 → 2 vCPU
```

There are 16 configured vCPUs on eight physical cores.

This may work well if most workloads are idle or lightly loaded at different times.

The administrator should monitor actual usage rather than simply looking at configuration.

Overcommitment should be based on measured workload behavior and acceptable performance, not on an arbitrary ratio such as "2:1 is always safe."

---

# 11. CPU Reservations, Limits, and Shares

Many virtualization platforms provide resource controls.

A **reservation** can define capacity that should be made available to a VM under contention.

A **limit** can restrict the maximum CPU capacity a VM is allowed to consume.

A **share** or priority mechanism can influence how competing VMs divide available CPU resources.

The conceptual model is:

```
Reservation → minimum resource commitment
Limit       → maximum resource consumption
Shares      → relative priority during contention
```

Terminology and behavior vary by platform.

These controls should be used carefully. A limit set too low can throttle an application unnecessarily. A reservation set too high can reduce the flexibility of the rest of the host.

---

# 12. CPU Pinning and Affinity

Advanced environments may allow a VM's vCPUs to be associated with particular physical cores.

This is often called CPU pinning or CPU affinity.

For example:

```
Physical cores:

0  1  2  3  4  5  6  7

Database VM → cores 4–7
```

This can be useful for specialized workloads requiring predictable CPU locality or performance characteristics.

However, pinning reduces the scheduler's flexibility. For ordinary server workloads, dynamic scheduling is usually preferable unless measurement demonstrates a specific need for pinning.

---

# 13. RAM Resource Management

Memory is one of the most important resources in a virtualization host because physical RAM is finite.

Suppose a host has 64 GB of RAM and four VMs are each assigned 16 GB:

```
VM1 → 16 GB
VM2 → 16 GB
VM3 → 16 GB
VM4 → 16 GB

Total → 64 GB
```

This consumes the entire physical capacity on paper, before accounting for the hypervisor, host services, storage caching, management tools, and operational headroom.

A host should therefore not normally be treated as having 100% of its physical RAM available for guest allocation.

---

# 14. Memory Overcommitment

Some platforms can allocate more virtual memory than the host physically owns.

For example:

```
Physical RAM → 32 GB

VM1 → 16 GB
VM2 → 16 GB
VM3 → 16 GB

Configured VM RAM → 48 GB
```

This can work when the VMs do not actually consume all of their configured memory at the same time.

However, if all workloads become memory-intensive, the host must manage memory pressure.

Possible techniques include memory ballooning, compression, page sharing, or swapping, depending on platform and configuration.

These mechanisms can help manage pressure but cannot create unlimited physical RAM.

---

# 15. Memory Ballooning

A memory balloon driver allows the hypervisor to request memory from a guest when the host needs it.

Conceptually:

```
Host memory pressure
        ↓
Hypervisor requests memory
        ↓
Balloon driver in guest
        ↓
Guest releases memory
        ↓
Host can use memory elsewhere
```

This mechanism requires cooperation between the guest operating system and virtualization platform.

It is useful because the hypervisor can reclaim memory without immediately forcing the guest to shut down.

---

# 16. Swapping and Severe Memory Pressure

Operating systems can use swap or paging when memory is insufficient.

For a guest:

```
Guest RAM pressure
      ↓
Guest swap
      ↓
Virtual disk
      ↓
Physical storage
```

If the host is also under memory pressure and begins swapping VM memory, performance can degrade dramatically.

The system may end up performing storage I/O for both guest-level and host-level memory management.

This is why physical RAM is particularly important for virtualization hosts.

A VM that is heavily swapping should not simply be given more vCPUs. The administrator should investigate memory pressure first.

---

# 17. Storage Resource Management

Storage has two major dimensions: **capacity** and **performance**.

Capacity tells us how much data can be stored.

Performance tells us how quickly that data can be accessed.

A database VM may have 500 GB of storage but still perform badly if the underlying datastore has high latency.

A backup server may require high sequential throughput.

A transactional database may require high IOPS and low latency.

Therefore, storage planning must consider:

```
Capacity
Latency
IOPS
Throughput
Reliability
Redundancy
Backup requirements
```

---

# 18. Virtual Disk Capacity

Suppose a VM has a 100 GB virtual disk.

The virtualization platform can implement that disk using different storage methods.

With **thick provisioning**, the physical storage may be reserved in advance.

With **thin provisioning**, physical storage consumption may grow as the guest actually writes data.

For example:

```
Virtual capacity → 100 GB
Actual data       → 20 GB
```

A thin-provisioned disk may consume substantially less physical storage than its maximum virtual capacity.

This can improve storage utilization, but it creates a capacity-management responsibility.

---

# 19. Thin Provisioning and Overprovisioning

Imagine a datastore with 1 TB of physical capacity.

The administrator creates:

```
VM1 → 500 GB thin disk
VM2 → 500 GB thin disk
VM3 → 500 GB thin disk
```

The configured virtual capacity is 1.5 TB.

This can work while actual usage remains below 1 TB.

However, if the VMs eventually fill their disks, the physical datastore cannot provide 1.5 TB.

This is **storage overprovisioning**.

The administrator must therefore monitor:

```
Physical free space
Allocated virtual capacity
Actual disk consumption
Growth rate
```

Thin provisioning is powerful, but it should never be treated as free storage.

---

# 20. IOPS, Throughput, and Latency

Storage performance requires more than a capacity measurement.

**IOPS**, or Input/Output Operations Per Second, describes how many individual storage operations can be completed.

**Throughput** describes the amount of data transferred over time.

**Latency** describes the time required for an operation to complete.

A workload performing many small random database operations may be highly sensitive to IOPS and latency.

A workload transferring large backup files may care more about sequential throughput.

This is why two storage systems with identical capacity can produce very different application performance.

---

# 21. Virtual Disk Formats

Common virtual disk formats include:

```
VDI
VMDK
VHD / VHDX
QCOW2
RAW
```

Different formats support different capabilities and are associated with different virtualization ecosystems.

For example, QCOW2 is widely used with QEMU.

The important conceptual model is:

```
Guest filesystem
      ↓
Guest block device
      ↓
Virtual disk
      ↓
Virtual disk format / block layer
      ↓
Datastore
      ↓
Physical storage
```

When diagnosing storage performance, the administrator should consider the entire chain rather than only the guest filesystem.

---

# 22. Thick and Thin Provisioning

A simple comparison is:

```
Thick:

100 GB virtual disk
        ↓
Physical capacity reserved in advance
```

versus:

```
Thin:

100 GB virtual disk
        ↓
Physical consumption grows with actual usage
```

Thick provisioning makes capacity planning easier because the reservation is explicit.

Thin provisioning can improve utilization and flexibility.

Neither is universally superior. The appropriate choice depends on workload behavior, storage architecture, performance requirements, and monitoring.

---

# 23. VM Snapshots

A snapshot records the state of a VM according to the snapshot mechanism provided by the virtualization platform.

A typical test workflow might be:

```
Working VM
    ↓
Create snapshot
    ↓
Install risky update
    ↓
Update fails
    ↓
Revert snapshot
```

Snapshots are excellent for temporary experimentation and rollback.

However, keeping snapshots for long periods can consume storage and complicate the disk chain, depending on the platform.

Snapshots should therefore normally be treated as short-term operational tools rather than permanent protection mechanisms.

---

# 24. Why a Snapshot Is Not a Backup

A snapshot and a backup solve different problems.

Suppose the VM and its snapshot are stored on the same datastore.

```
Physical datastore
├── VM disk
└── Snapshot data
```

If that datastore fails, both the original VM and snapshot may be lost.

A backup should provide an independent copy:

```
Production VM
      ↓
Backup system
      ↓
Separate backup storage
```

The simplest distinction is:

> **A snapshot helps you roll back. A backup helps you recover.**

A snapshot is useful for operational changes. A backup is designed for data protection.

---

# 25. Why Virtual Machines Need Backups

A virtual machine can fail for many reasons:

```
Accidental deletion
Filesystem corruption
Software failure
Administrator error
Malware or ransomware
Storage failure
Host failure
Physical disaster
```

Virtualization does not eliminate these risks.

In fact, virtualization can concentrate risk because many VMs may depend on the same host or datastore.

Backups provide a recovery mechanism independent of the original VM.

A good backup strategy should answer:

```
How much data can we lose?
How quickly must service return?
Where are backups stored?
How long are they retained?
How are they protected?
Have restores been tested?
```

---

# 26. RPO — Recovery Point Objective

RPO describes the maximum acceptable amount of data loss, usually expressed as time.

Suppose a server is backed up once every 24 hours.

If the server fails immediately before the next backup, nearly 24 hours of changes could be lost.

An organization requiring an RPO of one hour therefore needs more frequent protection or another technology such as replication or continuous data protection.

Conceptually:

```
Backup          Backup          Backup
  │               │               │
  └── 24 hours ───┘── 24 hours ───┘

Potential data loss ≈ backup interval
```

RPO is a business and technical requirement, not simply a backup setting.

---

# 27. RTO — Recovery Time Objective

RTO describes how quickly the service must be restored after a failure.

If a service fails at 10:00 and the RTO is two hours, the organization expects the service to be restored by approximately 12:00.

RTO affects recovery architecture.

A backup that requires six hours to restore cannot satisfy a two-hour RTO.

Therefore:

```
RPO → How much data can we lose?
RTO → How long can the service remain unavailable?
```

Both requirements should influence backup and disaster-recovery design.

---

# 28. Full VM Backups

A VM backup generally protects the VM's configuration and virtual disks, together with the metadata needed to reconstruct it.

A simplified workflow is:

```
Running VM
    ↓
Backup process
    ↓
Backup storage
```

The backup should ideally be stored outside the primary storage failure domain.

For example, storing the only backup of a VM on the same physical datastore as the VM provides little protection against datastore failure.

---

# 29. Crash-Consistent and Application-Consistent Backups

A **crash-consistent** backup approximates the state of a machine at the moment it stops unexpectedly.

An **application-consistent** backup coordinates with the guest and applications so that important application state can be flushed or quiesced before protection occurs.

For many ordinary workloads, crash consistency may be sufficient.

For databases and other stateful applications, application-aware backup may be preferable.

The administrator must therefore ask not only:

> "Was the VM backed up?"

but also:

> "Is the recovered application state acceptable?"

---

# 30. Guest Agents

Many virtualization platforms can install a guest agent inside the VM.

The virtualization platform can then communicate with the operating system.

Depending on the platform, guest-agent integration can support:

- clean shutdown;
    
- IP address discovery;
    
- filesystem information;
    
- backup coordination;
    
- quiescing operations;
    
- management features.
    

Conceptually:

```
Virtualization Platform
          │
          │ guest-agent communication
          ▼
Guest Operating System
          │
          ▼
Applications
```

This is another example of virtualization operating across multiple layers.

---

# 31. Backup Storage and Failure Domains

A backup should be protected from the same failure that could destroy the original.

Consider:

```
Host
├── VM
└── Backup
```

If the host's storage fails, both may be lost.

A stronger architecture is:

```
Virtualization Host
       │
       ▼
Backup Server
       │
       ▼
Separate Backup Storage
```

For even stronger protection, the backup can be copied to another location.

The well-known **3-2-1 backup principle** expresses the general idea:

```
3 copies of data
2 different storage/media types
1 copy off-site
```

The exact implementation can vary, but the key idea is to avoid having every copy fail for the same reason.

---

# 32. Backup Retention

A backup strategy also requires retention.

For example:

```
Daily → 7 days
Weekly → 4 weeks
Monthly → 12 months
```

Retention allows recovery from problems discovered long after they occurred.

Imagine that database corruption began three weeks ago but was detected today. If only seven days of backups exist, all available backups might already contain corrupted data.

Longer retention improves recovery options but consumes more storage.

Retention must therefore balance operational requirements, storage capacity, legal requirements, and business needs.

---

# 33. Backup Security

Backups often contain the same sensitive information as production systems.

A VM backup might contain:

```
Passwords
Database contents
Private keys
Configuration files
User information
Authentication tokens
```

Backup storage should therefore be protected with appropriate access controls and, where required, encryption.

Encryption in transit protects backup data during transfer.

Encryption at rest protects stored backup data.

Key management is equally important. A perfectly encrypted backup is useless if the organization loses the key needed to decrypt it.

---

# 34. Backup Verification

One of the most dangerous assumptions in system administration is:

> "The backup completed successfully, therefore we are protected."

A backup job can report success while the resulting data is incomplete, inaccessible, corrupted, or impossible to restore.

The only reliable way to validate recovery is to perform restore tests.

A simple test can look like:

```
Production VM
      ↓
Backup
      ↓
Restore into isolated environment
      ↓
Boot
      ↓
Verify filesystem
      ↓
Verify services
      ↓
Verify application data
```

The true measure of a backup system is not how successfully it creates backups. It is whether the organization can recover when a failure actually happens.

---

# 35. Restoring a Virtual Machine

A basic VM restoration process is:

```
Select backup
      ↓
Select recovery point
      ↓
Select destination
      ↓
Restore VM
      ↓
Verify virtual hardware
      ↓
Boot guest OS
      ↓
Verify network
      ↓
Verify filesystem
      ↓
Verify applications
      ↓
Return to service
```

Restoration should be performed carefully.

For example, restoring a VM with the same IP address while the original VM is still running can create an address conflict.

For this reason, administrators often restore into an isolated network first and verify the system before reconnecting it to production.

---

# 36. Full VM Recovery vs File Recovery

Not every incident requires a complete VM restore.

If an administrator accidentally deletes one file, restoring the entire machine may be unnecessary.

If the whole VM is destroyed, a full VM restore may be appropriate.

Therefore:

```
Small data loss
      ↓
File-level recovery

VM failure
      ↓
Full VM recovery

Host or site failure
      ↓
Disaster recovery
```

The recovery mechanism should match the scope of the incident.

---

# 37. Disaster Recovery

Backup is one component of disaster recovery.

Disaster recovery asks a broader question:

> "How do we restore the service if the original infrastructure is unavailable?"

Imagine that a physical virtualization host is destroyed.

The recovery plan may need to identify:

- replacement virtualization hosts;
    
- backup storage;
    
- VM recovery points;
    
- network configuration;
    
- DNS changes;
    
- authentication credentials;
    
- dependencies between services;
    
- recovery order;
    
- validation procedures.
    

A recovery environment might look like:

```
Primary Site
    │
    ├── Virtualization Hosts
    ├── Storage
    └── Network
          │
          │ backup / replication
          ▼
Recovery Site
    │
    ├── Virtualization Hosts
    ├── Backup Storage
    └── Network
```

Disaster recovery therefore concerns restoration of **operational capability**, not merely restoration of files.

---

# 38. Example: Designing a Web Server VM

Suppose a small organization needs a Linux VM for a low-traffic Web application.

A reasonable starting configuration might be:

```
VM name → web-app-01
CPU → 2 vCPU
RAM → 4 GB
Disk → 50 GB
Network → Server VLAN
```

Why not assign 16 vCPUs and 32 GB RAM immediately?

Because the workload does not currently justify it.

After deployment, suppose monitoring shows:

```
CPU average → 15%
RAM usage → 1.8 GB
Disk usage → 20 GB
Network → low
```

The VM appears appropriately sized.

If traffic later increases and CPU or RAM usage becomes consistently high, the administrator can resize the VM based on measured evidence.

This is **right-sizing**.

The objective is not to give every VM the maximum possible resources. The objective is to provide sufficient resources with reasonable headroom.

---

# 39. Example: Database VM

A database workload can behave very differently from a small Web server.

Suppose a database VM has:

```
8 vCPU
32 GB RAM
500 GB SSD
```

Monitoring shows:

```
CPU → 35%
RAM → 90%
Storage latency → high
```

Adding CPU would not necessarily solve the problem.

The evidence suggests that memory pressure and storage performance may be more important.

This illustrates a central performance principle:

> **Fix the bottleneck, not the resource that is easiest to increase.**

Good virtualization administration is based on observation and measurement.

---

# 40. Practical Lab 1 — Create a Virtual Server

Create a Linux server VM using VirtualBox, Proxmox VE, or another available hypervisor.

A simple starting configuration is:

```
2 vCPU
2–4 GB RAM
20–30 GB disk
1 vNIC
```

Install a server-oriented Linux distribution.

After installation, run:

```
lscpu
free -h
lsblk
ip addr
ip route
```

For each command, explain what virtual resource you are observing and where that resource ultimately comes from.

For example:

```
Linux /dev/sda
      ↓
Virtual disk
      ↓
Virtual disk format / block device
      ↓
Datastore
      ↓
Physical SSD/HDD
```

Do not stop at "the VM has a disk." Try to understand the complete path.

---

# 41. Practical Lab 2 — Experiment with CPU Allocation

Create a VM with one vCPU.

Inside the VM:

```
nproc
lscpu
```

Record the result.

Shut down the VM, increase it to two vCPUs, and boot again.

Run the same commands.

The guest should now observe a different virtual CPU configuration.

The physical host has not gained a CPU core. The hypervisor has changed the virtual hardware presented to the guest.

Now create several VMs and generate CPU load. Observe how simultaneous workloads affect the physical host and the guests.

The purpose is to connect vCPU configuration with actual resource contention.

---

# 42. Practical Lab 3 — Experiment with Memory

Create a VM with 2 GB RAM.

Inside it:

```
free -h
```

Record the available memory.

Shut down the VM and increase the allocation to 4 GB.

Boot and repeat the command.

Then consider the physical host. If the host has 8 GB RAM and three VMs are each configured with 4 GB, the virtual allocation is 12 GB.

Ask:

> What happens if all three VMs actually require their full allocation at the same time?

This is the practical problem behind memory overcommitment.

---

# 43. Practical Lab 4 — Experiment with Storage

Create a VM with a 20 GB virtual disk.

Inside Linux:

```
lsblk
df -h
```

Understand the difference between the block device and the filesystem.

Then compare the disk capacity visible inside the guest with the physical storage consumed on the host.

If the virtual disk is thin-provisioned, create additional files in the VM and observe how physical storage consumption changes.

This demonstrates the difference between **virtual capacity** and **physical consumption**.

---

# 44. Practical Lab 5 — Experiment with Snapshots

Create a test VM and write:

```
echo "before snapshot" > ~/test.txt
```

Create a snapshot.

Then modify the file:

```
echo "after snapshot" > ~/test.txt
```

Revert the snapshot.

Observe what happens to the file.

Then answer:

> If the physical datastore were destroyed, would this snapshot protect the VM?

This question should lead you to the distinction between rollback and backup.

---

# 45. Practical Lab 6 — Backup and Restore

Using Proxmox VE or another platform supporting VM backups, create a test VM and back it up.

Verify that the backup exists on the intended backup storage.

Then restore the VM into a test environment.

After restoration, verify:

```
hostname
ip addr
df -h
systemctl --failed
```

Also verify that expected files and applications are present.

Do not consider the test successful merely because the restore command says "completed."

The real objective is to demonstrate that the recovered workload is operational.

---

# 46. Practical Lab 7 — Simulate Failure and Recovery

Create a small test service inside a VM.

Back up the VM.

Then deliberately break a non-critical configuration or remove a test file.

Observe the failure.

Restore the VM or the required data from the backup.

Verify that the service and data are operational again.

The lifecycle should look like:

```
Deploy
   ↓
Operate
   ↓
Backup
   ↓
Failure
   ↓
Restore
   ↓
Verify
```

This is closer to real server administration than simply installing a VM.

---

# 47. Troubleshooting a Slow Virtual Server

When a VM is slow, do not immediately increase CPU, RAM, and disk.

First identify the bottleneck.

A useful diagnostic structure is:

```
CPU
 ├── High utilization?
 ├── Contention?
 └── Is workload CPU-bound?

RAM
 ├── Memory pressure?
 ├── Swap?
 └── Host exhaustion?

Storage
 ├── High latency?
 ├── High IOPS?
 ├── High throughput?
 └── Datastore nearly full?

Network
 ├── Packet loss?
 ├── High latency?
 ├── Routing problem?
 └── Bridge/VLAN/firewall problem?
```

A VM may look healthy from inside while the physical host is overloaded.

For example:

```
Guest CPU → 30%
Guest RAM → 50%

Host CPU → 95%
Host RAM → 98%
Datastore latency → high
```

The problem may therefore exist outside the guest.

This is why virtualization administrators must inspect several layers.

---

# 48. The Four Layers of Virtual Server Management

A useful mental model divides the environment into four layers.

### Layer 1 — Guest

The operating system and applications inside the VM:

```
Linux
Windows Server
Web server
Database
DNS
```

### Layer 2 — Virtual Hardware

What the guest sees:

```
vCPU
Virtual RAM
Virtual Disk
vNIC
Firmware
```

### Layer 3 — Virtualization Platform

The components managing the virtual environment:

```
Hypervisor
Virtual switches / bridges
Virtual storage
VM configuration
Snapshots
Backup integration
```

### Layer 4 — Physical Infrastructure

The actual hardware:

```
Physical CPU
Physical RAM
SSD / HDD
NIC
Physical switch
Power
Cooling
```

The complete chain is:

```
Guest Applications
        ↓
Guest Operating System
        ↓
Virtual Hardware
        ↓
Hypervisor / Virtualization Platform
        ↓
Physical Infrastructure
```

When troubleshooting, determine which layer is responsible before changing configuration.

---

# 49. Common Beginner Mistakes

A frequent mistake is to give every VM excessive resources.

For example:

```
Every VM → 8 vCPU / 32 GB RAM / 500 GB disk
```

This can waste resources and reduce host capacity.

Another mistake is assuming that vCPUs are equivalent to dedicated physical cores. They are not necessarily dedicated.

Another mistake is ignoring host overhead. If all physical RAM is allocated to VMs, the hypervisor and host services still require memory.

Another mistake is confusing virtual disk capacity with physical storage consumption, especially when thin provisioning is used.

Another mistake is treating snapshots as backups.

Another mistake is assuming that a successful backup job proves recoverability. A backup must be tested through restoration.

Finally, administrators often attempt to solve performance problems by adding resources without first identifying the actual bottleneck.

---

# 50. Questions for Understanding

## Question 1

Why does server virtualization allow multiple server workloads to share one physical machine?

## Question 2

What is the difference between a vCPU and a physical CPU core?

## Question 3

Why can a VM with eight vCPUs still experience CPU contention?

## Question 4

Why is assigning more RAM not automatically the correct solution when a VM is slow?

## Question 5

What is the difference between virtual disk capacity and physical storage consumption?

## Question 6

Why is thin provisioning useful?

## Question 7

What is the main risk of excessive storage overprovisioning?

## Question 8

Why is a snapshot not a replacement for a backup?

## Question 9

What do RPO and RTO measure?

## Question 10

Why must backups be tested through restoration?

---

# 51. Reasoning Exercises

## Exercise 1 — CPU Planning

A physical host has eight CPU cores.

You want to deploy:

```
Web VM       → 2 vCPU
Database VM  → 4 vCPU
DNS VM       → 1 vCPU
Monitoring   → 2 vCPU
```

The total allocation is greater than the physical core count.

Is this automatically incorrect?

Explain your reasoning using CPU overcommitment, workload behavior, and contention.

---

## Exercise 2 — Memory Planning

A host has 32 GB of RAM.

You configure:

```
VM1 → 8 GB
VM2 → 8 GB
VM3 → 8 GB
VM4 → 8 GB
```

Is this necessarily safe?

What additional information do you need?

Your answer should consider host overhead, workload behavior, memory pressure, and operational headroom.

---

## Exercise 3 — Storage Planning

A datastore has 1 TB of physical capacity.

You create:

```
VM1 → 500 GB thin disk
VM2 → 500 GB thin disk
VM3 → 500 GB thin disk
```

Explain why this may work initially but become dangerous later.

What should the administrator monitor?

---

## Exercise 4 — Snapshot or Backup?

A production VM receives a major software upgrade. The administrator creates a snapshot immediately before the upgrade and says:

> "We now have a backup."

Is this correct?

Explain precisely why or why not.

---

## Exercise 5 — Recovery Design

A company says:

> "Our application cannot lose more than one hour of data, and after a failure it must be operational again within two hours."

Identify the RPO and RTO.

Then explain what these requirements imply for backup frequency and recovery architecture.

---

# 52. Mini Project — Build a Small Virtual Server Infrastructure

Build a small virtualized infrastructure containing at least three VMs.

A possible design is:

```
VM 1 → Linux Web Server
VM 2 → Linux DNS Server
VM 3 → Linux Client
```

Connect them through a virtual network.

The Web server should provide a simple service that the client can access.

Then configure a backup of the Web server.

Your project should follow this lifecycle:

```
Planning
   ↓
VM Creation
   ↓
Resource Allocation
   ↓
OS Installation
   ↓
Network Configuration
   ↓
Service Deployment
   ↓
Backup
   ↓
Failure Simulation
   ↓
Restore
   ↓
Verification
```

Document why you selected the CPU, RAM, disk, and network settings.

Then explain what would happen if the physical host failed.

Finally, restore the Web server and demonstrate that the service is operational again.

The purpose is to make you think about the complete lifecycle of a virtual server rather than treating VM creation as the end of the task.

---

# 53. Final Synthesis

Server virtualization transforms physical infrastructure into a platform capable of hosting multiple logical server environments.

A virtual machine receives vCPUs, virtual memory, virtual disks, and virtual network interfaces. These resources are managed by the virtualization layer and ultimately depend on physical CPU, RAM, storage, and networking.

The first major responsibility of a virtualization administrator is therefore **resource management**.

CPU allocation determines how much virtual processor capacity is presented to the guest. Because physical CPU capacity is finite, multiple VMs can compete for the same resources.

Memory allocation determines how much memory the guest can use. Memory pressure can become particularly serious because swapping and memory reclamation can cause significant performance degradation.

Storage must be considered from both a capacity and performance perspective. A VM can have enough disk capacity while still suffering from high latency or insufficient I/O performance.

Virtual machine configuration also includes identity, operating-system compatibility, firmware, virtual hardware, network interfaces, and other parameters.

The second major responsibility is **lifecycle management**.

A virtual server must not simply be created and forgotten. It must be monitored, updated, protected, backed up, recovered, and eventually retired.

This is where snapshots and backups must be clearly distinguished.

A snapshot is useful for short-term rollback and testing. A backup is designed to provide a recoverable copy of data if the original environment fails.

A strong backup strategy considers RPO, RTO, retention, storage independence, security, and restore testing.

The complete lifecycle can be represented as:

```
              Plan
                ↓
             Create
                ↓
             Configure
                ↓
             Monitor
                ↓
            Right-size
                ↓
             Protect
                ↓
             Backup
                ↓
             Restore
                ↓
             Verify
                ↓
             Retire
```

Virtualization does not remove traditional systems-administration responsibilities. It adds another abstraction layer.

A physical server administrator asks:

> "How are my CPU, memory, disks, and network interfaces performing?"

A virtualization administrator must also ask:

> "How are those physical resources being allocated to VMs, and how are the VMs consuming the virtual resources presented to them?"

That additional layer is what makes virtualization powerful and what makes virtualization administration a discipline in its own right.

---

# 54. Essential Knowledge Before Moving On

Before continuing to the next chapter, you should be able to explain what happens when a virtual machine is created.

You should be able to describe how the administrator defines virtual CPUs, memory, storage, networking, firmware, and other virtual hardware.

You should understand that vCPUs are scheduled onto physical CPU resources and that virtual memory ultimately depends on physical RAM.

You should understand the difference between virtual disk capacity and physical storage consumption, especially with thin provisioning.

You should be able to explain the basic meaning of IOPS, throughput, and latency.

You should be able to distinguish:

```
Snapshot
    ↓
Short-term rollback / testing

Backup
    ↓
Data protection / recovery
```

You should understand:

```
RPO → How much data loss is acceptable?

RTO → How quickly must service be restored?
```

Most importantly, you should be able to reason through the complete virtualization stack:

```
Guest Applications
        ↓
Guest Operating System
        ↓
Virtual Hardware
        ↓
Hypervisor / Virtualization Platform
        ↓
Physical Hardware
```

When a VM is slow, disconnected, corrupted, or unavailable, this layered model should guide your investigation.

The next step is to go deeper into how virtualization platforms actually implement CPU scheduling, memory virtualization, virtual storage, and virtual networking.