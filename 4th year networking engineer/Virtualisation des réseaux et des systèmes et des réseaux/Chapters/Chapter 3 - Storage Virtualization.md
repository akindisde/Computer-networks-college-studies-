# Chapter 3 — Storage Virtualization

## Introduction

Storage virtualization is the abstraction of physical storage resources into logical storage resources that can be allocated to virtual machines without exposing the physical implementation directly to the guest operating system.

In a physical server, an operating system may access a local SSD directly. In a virtualized environment, the guest might see `/dev/sda` as a 100 GB disk even though that disk is actually implemented as a VDI, VMDK, QCOW2, logical volume, ZFS volume, SAN LUN, NFS-backed file, or distributed storage volume.

The fundamental model is:

```
Guest Application
      ↓
Guest Filesystem
      ↓
Virtual Block Device
      ↓
Virtual Disk
      ↓
Hypervisor Storage Layer
      ↓
Datastore / Storage Pool
      ↓
Physical or Network Storage
```

This abstraction is powerful because virtual disks can be created, resized, migrated, cloned, backed up, and attached to different virtual machines much more flexibly than physical disks. However, abstraction also adds layers. A storage problem visible inside a VM may originate in the filesystem, virtual disk, datastore, storage network, RAID layer, controller, or physical disks.

This chapter develops storage virtualization from the foundations upward. We will study virtual disks, provisioning, shared storage, storage performance, redundancy, RAID, distributed storage, monitoring, troubleshooting, and practical administration.

---

# 1. Learning Objectives

By the end of this chapter, you should be able to explain what storage virtualization is and distinguish physical storage from virtual storage.

You should understand how a virtual disk is presented to a guest operating system, how virtual disk formats and controllers work, and how storage capacity can be allocated using thick or thin provisioning.

You should be able to expand virtual disks safely and understand why increasing a virtual disk does not necessarily increase the filesystem size automatically.

You should understand shared storage, including the difference between file-based and block-based approaches, and why shared storage is important for virtualization clusters, migration, and high availability.

You should also understand storage redundancy and the purpose of RAID levels such as RAID 0, RAID 1, RAID 5, RAID 6, and RAID 10.

Finally, you should be able to distinguish redundancy, backup, and disaster recovery, and troubleshoot storage problems by following the complete storage stack.

---

# 2. What Is Storage Virtualization?

Storage virtualization means presenting logical storage resources independently of their underlying physical implementation.

Consider a physical machine containing one SSD:

```
Physical Server
      │
      └── Physical SSD
```

The operating system accesses the physical storage directly.

In a virtualized environment, the architecture becomes:

```
Physical Host
      │
   Hypervisor
      │
 Storage Layer
      │
 Virtual Disk
      │
 Guest Operating System
```

The guest sees a disk, but that disk is virtual. For example, Linux might see:

```
/dev/sda
```

while the hypervisor might store it as:

```
server01.qcow2
```

or:

```
server01.vdi
```

Alternatively, it could be backed by a logical volume, a ZFS volume, a SAN LUN, an NFS datastore, or distributed storage.

The guest normally does not need to know which implementation is being used. That is the central abstraction of virtual storage.

---

# 3. Physical Storage, Virtual Storage, and Datastores

A useful way to understand storage virtualization is to separate the layers.

At the bottom are physical devices:

```
SSD 1
SSD 2
SSD 3
SSD 4
```

These may be combined into a storage pool, RAID array, ZFS pool, or another storage system:

```
Physical Disks
      ↓
Storage Pool
      ↓
Datastore
      ↓
Virtual Disks
      ↓
Virtual Machines
```

A datastore is a location or storage resource that the virtualization platform uses to hold VM disks and related data.

A storage pool does not automatically imply redundancy. A pool could be based on one disk, several disks without protection, RAID, ZFS, or distributed storage. Always investigate the underlying architecture.

---

# 4. Why Virtualize Storage?

The first major advantage is flexibility. A virtual disk is a logical object rather than a physical device tied permanently to one machine.

The second advantage is abstraction. The guest operating system can use a normal block device without managing the details of the physical storage infrastructure.

The third advantage is resource pooling. Physical capacity can be consolidated into storage pools and allocated to workloads as required.

The fourth advantage is mobility. When storage is shared or distributed appropriately, a VM can potentially move between physical hosts without manually moving a physical disk.

The fifth advantage is centralized administration. Administrators can monitor and manage storage through the virtualization platform instead of configuring every physical disk independently.

Storage virtualization therefore separates the question:

> "Where is the data physically stored?"

from:

> "Which logical storage resource does the VM need?"

---

# 5. Virtual Disks

A virtual disk is a logical block device presented to a virtual machine.

From the guest's perspective, it behaves much like a physical disk:

```
VM
│
└── Virtual Disk: 100 GB
       │
       ▼
Guest OS
       │
       └── /dev/sda
```

The guest can partition it:

```
/dev/sda
├── /dev/sda1
└── /dev/sda2
```

and create filesystems on those partitions.

The important distinction is that the guest's `/dev/sda` is not necessarily a physical disk. It is a virtual representation backed by the virtualization platform.

---

# 6. Virtual Disk Formats

Common virtual disk formats include:

```
VDI
VMDK
VHD / VHDX
QCOW2
RAW
```

VDI is strongly associated with VirtualBox. VMDK originated in the VMware ecosystem and is supported by multiple virtualization products. VHD and VHDX are strongly associated with Microsoft virtualization technologies. QCOW2 is common with QEMU/KVM. RAW is a relatively direct block representation.

Do not memorize these formats as isolated names. The important concept is that a virtual disk format defines how a virtual disk is represented and managed by the virtualization environment.

Some formats support features such as snapshots, copy-on-write, sparse allocation, and metadata. These features can affect performance, compatibility, and storage consumption.

---

# 7. Virtual Disk Controllers

A VM needs a virtual controller through which the guest accesses its disks.

Common controller/device families include:

```
IDE
SATA
SCSI
SAS
VirtIO
NVMe
```

The available choices depend on the hypervisor and guest operating system.

Traditional emulated devices can provide compatibility because the guest recognizes familiar hardware. Paravirtualized devices such as VirtIO can reduce emulation overhead because the guest uses drivers specifically designed for virtualization.

Conceptually:

```
Emulated:

Guest → Virtual Device Emulation → Storage Backend


Paravirtualized:

Guest → VirtIO Driver → Hypervisor Storage Layer → Backend
```

For production workloads, the choice of virtual storage device can influence performance and compatibility.

---

# 8. Fixed and Dynamically Allocated Virtual Disks

A virtual disk can often be allocated as fixed-size or dynamically allocated.

Suppose you create a 100 GB virtual disk.

With a fixed allocation, the host may reserve approximately the full capacity immediately:

```
Virtual capacity → 100 GB
Physical consumption → approximately 100 GB
```

With dynamic allocation:

```
Virtual capacity → 100 GB
Physical consumption → initially 5 GB
```

As the guest writes data, physical consumption can grow:

```
Virtual capacity → 100 GB
Physical consumption → 20 GB
```

and later:

```
Virtual capacity → 100 GB
Physical consumption → 70 GB
```

This is commonly described as thin provisioning or dynamic allocation.

---

# 9. Thin Provisioning

Thin provisioning separates logical capacity from physical capacity.

Imagine a datastore with 1 TB of physical capacity:

```
Physical datastore → 1 TB
```

You create:

```
VM1 → 500 GB
VM2 → 500 GB
VM3 → 500 GB
```

The configured virtual capacity is 1.5 TB, even though only 1 TB exists physically.

This can work as long as the VMs do not consume the entire logical capacity simultaneously.

Thin provisioning improves utilization, but it introduces a critical risk: the physical datastore can become full while the VMs still believe that they have free logical capacity.

Therefore:

> Thin provisioning does not create physical storage capacity.

It requires capacity monitoring, alerting, and growth planning.

---

# 10. Thick Provisioning

With thick provisioning, physical capacity is reserved more explicitly in advance.

For a 100 GB virtual disk:

```
Virtual capacity → 100 GB
Physical allocation → approximately 100 GB
```

Even if the guest only stores 10 GB of data, much of the physical capacity may remain reserved.

The advantage is predictable capacity allocation. The disadvantage is lower utilization when large amounts of the virtual disk remain unused.

Neither thick nor thin provisioning is universally superior. The choice depends on workload behavior, storage architecture, operational requirements, and monitoring.

---

# 11. Capacity Versus Performance

Storage capacity and storage performance are different properties.

A storage system can have enormous capacity but poor performance.

Three important performance concepts are:

**Latency** is the time required for an I/O operation to complete.

**IOPS**, or Input/Output Operations Per Second, measures how many storage operations can be performed per second.

**Throughput** measures the amount of data transferred over time.

A transactional database often cares strongly about latency and random IOPS. A backup workload may care more about sequential throughput.

Therefore:

> A VM can have sufficient disk capacity and still have inadequate storage performance.

---

# 12. Example: Capacity Is Not Performance

Imagine:

```
Storage A → 4 TB HDD
Storage B → 1 TB NVMe SSD
```

Storage A has greater capacity, but Storage B may be substantially faster for workloads requiring low latency and high IOPS.

This is why storage design begins with workload requirements rather than simply asking:

> "How many gigabytes do we need?"

A better analysis asks:

```
How much capacity?
How much IOPS?
How much throughput?
What latency is acceptable?
How important is redundancy?
How quickly will the data grow?
```

---

# 13. Creating a Virtual Disk for an Operating System

When installing an operating system inside a VM, the installer sees the virtual disk.

For example:

```
Virtual Disk → 32 GB
```

Linux may report:

```
/dev/sda
```

The installer might create:

```
/dev/sda1 → EFI System Partition
/dev/sda2 → root filesystem
/dev/sda3 → swap
```

The guest's storage hierarchy is therefore:

```
Virtual Disk
      ↓
Partition Table
      ↓
Partitions
      ↓
Filesystems
      ↓
Files
```

The host has another hierarchy underneath it:

```
Virtual Disk
      ↓
Virtual Disk Format / Block Layer
      ↓
Datastore
      ↓
Physical Storage
```

These two hierarchies meet at the virtual disk abstraction.

---

# 14. Expanding a Virtual Disk

Expanding a virtual disk is a common administrative task.

Suppose the VM has:

```
Virtual disk → 40 GB
```

and you increase it to:

```
Virtual disk → 80 GB
```

This does not necessarily mean the guest filesystem immediately becomes 80 GB.

The guest may actually contain:

```
Disk       → 80 GB
Partition  → 40 GB
Filesystem → 40 GB
```

The new capacity exists at the virtual-disk layer, but the guest partition and filesystem have not yet consumed it.

The administrator may therefore need to extend:

```
Partition
Logical Volume, if present
Filesystem
```

The exact procedure depends on the storage layout.

---

# 15. Linux Disk Expansion Example

Start by inspecting the block devices:

```
lsblk
```

Then inspect filesystem capacity:

```
df -h
```

If LVM is used, also inspect:

```
sudo pvs
sudo vgs
sudo lvs
```

Determine the filesystem type:

```
df -T
```

Depending on the architecture, tools such as these may be required:

```
growpart
lvextend
resize2fs
xfs_growfs
```

For example, ext4 filesystems commonly use `resize2fs`, while XFS is commonly expanded with `xfs_growfs`.

Never execute filesystem-resizing commands blindly. First identify:

```
Disk
Partition
LVM
Filesystem
Mount point
```

Storage administration is about understanding the layers before modifying them.

---

# 16. Shrinking Virtual Disks

Shrinking is significantly more dangerous than expanding.

If a filesystem occupies 30 GB and the virtual disk is reduced to 20 GB, data will be destroyed.

A safe shrink operation generally proceeds from the inside outward:

```
Filesystem
      ↓
Logical Volume
      ↓
Partition
      ↓
Virtual Disk
```

The exact order depends on the architecture.

Never reduce a virtual disk simply because the guest currently uses less space.

Expansion is usually straightforward. Shrinking requires careful planning, supported tools, backups, and verification.

---

# 17. Multiple Virtual Disks

A VM can contain multiple virtual disks.

For example:

```
VM
├── Disk 1 → 40 GB → Operating system
├── Disk 2 → 200 GB → Application data
└── Disk 3 → 500 GB → Backup or archive data
```

Separating data can simplify management and monitoring.

For example, if the operating-system filesystem becomes full, application data on another disk may remain available.

Separate disks can also be useful when different workloads have different performance requirements.

However, adding disks does not automatically improve performance. The underlying storage system remains the physical bottleneck if it cannot supply the required I/O.

---

# 18. Shared Storage

Shared storage is storage accessible by multiple physical hosts.

Without shared storage:

```
Host A → Local VM storage
Host B → Local VM storage
```

A VM stored on Host A's local disk cannot simply be accessed by Host B.

With shared storage:

```
             Shared Storage
             /      |                  /       |               Host A    Host B    Host C
```

Multiple hosts can access the same storage resource.

This is especially useful in virtualization clusters because VM disks can remain accessible when workloads move between hosts.

---

# 19. Why Shared Storage Matters

Suppose a VM is running on Host A and its virtual disk is stored only on Host A's local disk.

If Host A fails:

```
Host A failure
      ↓
VM disk unavailable
      ↓
Host B cannot directly start VM
```

Now consider shared storage:

```
Host A ─┐
Host B ─┼── Shared Storage
Host C ─┘
```

If Host A fails, Host B can potentially access the VM's disk and restart the VM, provided that the cluster, networking, configuration, and high-availability mechanisms are correctly configured.

Shared storage is therefore an important component of many high-availability designs.

---

# 20. File-Based and Block-Based Shared Storage

Shared storage can expose different abstractions.

NFS is a common file-based approach:

```
Hypervisor
    ↓
NFS
    ↓
NFS Server
    ↓
Physical Storage
```

iSCSI is a common block-based approach:

```
Hypervisor
    ↓
iSCSI
    ↓
Storage Target
    ↓
Physical Storage
```

Fibre Channel is another block-storage technology frequently used in enterprise environments.

The distinction matters because file and block storage have different management models, performance characteristics, locking behavior, and failure modes.

---

# 21. NAS, SAN, and Distributed Storage

A NAS generally provides file-based storage over a network.

A SAN commonly provides block storage to hosts.

Distributed storage spreads storage functionality across several nodes.

Examples include:

```
NAS → NFS
SAN → iSCSI / Fibre Channel
Distributed → Ceph
```

The correct choice depends on:

```
Performance requirements
Budget
Number of hosts
Availability requirements
Network architecture
Operational expertise
Data growth
```

There is no single storage technology that is best for every virtualization environment.

---

# 22. Shared Storage and Network Design

Once VM disks are stored on network storage, the network becomes part of the storage path:

```
VM
 ↓
Hypervisor
 ↓
Storage Network
 ↓
Storage Server / Cluster
 ↓
Physical Disks
```

A network problem can therefore appear as a storage problem.

Storage networks should be designed with attention to:

```
Bandwidth
Latency
Redundant links
Redundant switches
VLANs
MTU consistency
Authentication
Access control
Monitoring
```

In larger environments, management, VM traffic, and storage traffic may be logically or physically separated.

---

# 23. Storage Network Separation

A simple architecture may use:

```
Management Network
        ↓
Hypervisor administration

VM Network
        ↓
Application traffic

Storage Network
        ↓
NFS / iSCSI / replication
```

This separation can improve security, predictability, and troubleshooting.

Physical separation is not always required. VLANs and dedicated interfaces can provide logical separation depending on the environment.

The key is to understand the traffic flows and prevent storage traffic from becoming an uncontrolled competitor with application traffic.

---

# 24. VM Migration and Shared Storage

Shared storage is especially useful for VM migration.

A simplified architecture is:

```
                 Shared Storage
                      │
             ┌────────┴────────┐
             │                 │
           Host A            Host B
             │                 │
            VM ───────────────►
                 Migration
```

The VM can move from Host A to Host B while its virtual disk remains on shared storage.

This is useful for:

```
Maintenance
Load balancing
High availability
Hardware replacement
Cluster management
```

Live migration also depends on CPU compatibility, virtual networking, cluster configuration, and hypervisor capabilities.

---

# 25. Local Storage Versus Shared Storage

Local storage can offer:

```
Low latency
Simple design
Potentially lower cost
No storage-network dependency
```

But it can also make migration and failover more difficult.

Shared storage can offer:

```
Host mobility
Centralized storage
Cluster integration
Simpler VM relocation
```

But it introduces:

```
Network dependency
Additional infrastructure
Potential storage bottlenecks
More complex failure domains
```

Modern platforms can also use distributed storage built from local disks, combining some benefits of both approaches.

---

# 26. Storage Redundancy

Storage redundancy means having additional physical resources or copies so that the failure of a component does not necessarily make data unavailable.

A traditional example is RAID:

```
Several Physical Disks
          ↓
         RAID
          ↓
Logical Storage Volume
          ↓
Virtualization Platform
```

The virtualization layer may see one logical storage resource even though multiple physical disks support it.

Redundancy improves availability and, depending on the design, can protect against physical disk failure.

---

# 27. RAID 0

RAID 0 uses striping without redundancy.

Data is distributed across multiple disks:

```
Disk 1       Disk 2
  A1           A2
  A3           A4
  A5           A6
```

This can improve aggregate performance and uses most of the combined capacity.

However, if any disk fails, the array fails.

```
RAID 0
Performance → potentially high
Capacity efficiency → high
Fault tolerance → none
```

RAID 0 is therefore not a redundant storage solution.

---

# 28. RAID 1

RAID 1 uses mirroring.

The same information is stored on two disks:

```
Disk 1       Disk 2
  A1           A1
  A2           A2
  A3           A3
```

If one disk fails, the other can continue serving the data.

For a two-disk mirror:

```
Usable capacity ≈ 50% of raw capacity
Fault tolerance → one disk
```

RAID 1 is simple and provides strong protection against a single disk failure.

---

# 29. RAID 5

RAID 5 combines striping with distributed parity.

A simplified representation is:

```
Disk 1   Disk 2   Disk 3
Data     Data     Parity
Data     Parity   Data
Parity   Data     Data
```

Parity permits the array to reconstruct data after one disk failure.

Conceptually:

```
Minimum disks → 3
Fault tolerance → 1 disk
```

RAID 5 uses capacity efficiently compared with mirroring, but write performance and rebuild behavior must be considered.

With large modern disks, rebuilds can be lengthy and stressful for the remaining drives.

---

# 30. RAID 6

RAID 6 uses dual parity.

It can tolerate two disk failures:

```
Minimum disks → 4
Fault tolerance → 2 disks
```

The additional parity provides greater protection for larger arrays, but it requires more capacity and can introduce additional write overhead.

RAID 6 is often appropriate when protection against a second disk failure during rebuild is important.

---

# 31. RAID 10

RAID 10 combines mirroring and striping.

Conceptually:

```
Mirror A             Mirror B
Disk 1  Disk 2       Disk 3  Disk 4
   │       │            │       │
   └─mirror─┘           └─mirror─┘
          \               /
             striping
```

RAID 10 typically requires at least four disks.

It offers strong performance and redundancy, although usable capacity is approximately half of raw capacity.

The exact number of failures that can be tolerated depends on which disks fail. Multiple failures can be survived if they occur in different mirror groups.

---

# 32. RAID Comparison

|RAID|Technique|Typical Fault Tolerance|Main Strength|
|---|---|---|---|
|RAID 0|Striping|None|Performance/capacity|
|RAID 1|Mirroring|One disk per mirror|Simplicity|
|RAID 5|Striping + parity|One disk|Capacity efficiency|
|RAID 6|Striping + dual parity|Two disks|Higher protection|
|RAID 10|Mirroring + striping|Depends on failure pattern|Performance + redundancy|

Actual performance and usable capacity depend on implementation, controller, workload, disk type, and filesystem/storage software.

---

# 33. RAID Is Not Backup

This is one of the most important storage concepts.

Suppose a RAID 1 array contains a database:

```
Disk 1 → Database
Disk 2 → Mirror
```

If Disk 1 fails, Disk 2 can continue serving the data.

But if an administrator accidentally deletes the database:

```
Accidental deletion
       ↓
Disk 1 → deleted
Disk 2 → deleted
```

The deletion is mirrored.

RAID protects against certain hardware failures. It does not inherently protect against:

```
Accidental deletion
Ransomware
Malware
Application corruption
Administrator mistakes
Fire
Flood
Theft
Site-wide disaster
```

Backup is therefore still necessary.

---

# 34. Hardware RAID and Software RAID

RAID can be implemented through dedicated hardware:

```
Hypervisor
    ↓
RAID Controller
    ↓
Physical Disks
```

or software:

```
Operating System / Storage Software
             ↓
        Software RAID
             ↓
        Physical Disks
```

Hardware RAID can simplify the presentation of storage to the operating system.

Software-defined storage can provide more flexible functionality and may integrate checksums, snapshots, replication, compression, and other features.

Examples include Linux MD RAID, ZFS, and distributed storage platforms.

---

# 35. ZFS and Storage Pools

ZFS combines filesystem and storage-management functionality.

A simplified model is:

```
Physical Disks
      ↓
ZFS Pool
      ↓
Datasets / zvols
      ↓
Virtualization Storage
```

ZFS can provide checksumming, snapshots, replication mechanisms, compression, and redundant layouts such as mirrors and RAID-Z.

The important lesson is that modern storage is not limited to traditional hardware RAID.

---

# 36. Distributed Storage

Distributed storage spreads storage across several physical nodes.

For example:

```
Node A              Node B              Node C
Disks               Disks               Disks
  │                   │                   │
  └───────────────────┼───────────────────┘
                      ↓
             Distributed Storage
                      ↓
                  VM Storage
```

A distributed platform can replicate data between nodes and provide storage to virtualization hosts.

Ceph is a well-known example.

Distributed storage can make local disks from several servers behave as a larger shared storage system.

However, it introduces requirements around:

```
Network bandwidth
Network latency
Node availability
Replication
Consistency
Failure domains
Monitoring
Recovery
```

---

# 37. Failure Domains

Redundancy only protects against failures within the architecture's design assumptions.

Suppose two logical copies are stored on the same physical disk:

```
Copy 1 ─┐
Copy 2 ─┼── Same physical disk
        └── Disk failure → both lost
```

Likewise:

```
Replica 1 → Host A
Replica 2 → Host A

Host A failure → both unavailable
```

A stronger design separates replicas across independent failure domains:

```
Replica 1 → Host A
Replica 2 → Host B
```

For site-level protection:

```
Primary Site → Secondary Site
```

The key question is:

> "What failure am I trying to survive?"

Redundancy should be designed around actual failure domains.

---

# 38. Multipathing

Some storage architectures provide multiple paths between a host and storage.

For example:

```
             Storage
             /               Path A   Path B
           /                Switch A     Switch B
          \           /
             Host
```

If one path fails, another may remain available.

This is called multipathing.

Multipathing can improve availability and, depending on the implementation, may also improve performance.

It is common in enterprise storage environments and is particularly useful when storage access is a critical dependency for many VMs.

---

# 39. Storage Availability, Backup, and Disaster Recovery

These are three different goals.

**Availability** aims to keep services running during certain failures.

Examples:

```
RAID
Multipathing
Redundant controllers
```

**Backup** provides recoverable historical copies.

Examples:

```
VM backups
Off-site copies
Immutable backup repositories
```

**Disaster recovery** restores service after major infrastructure failure.

Examples:

```
Replication
Secondary site
Recovery cluster
Documented recovery procedures
```

A system can have excellent RAID and still have poor data protection.

---

# 40. Example: Web Server Storage

Suppose a low-traffic Web server requires:

```
OS → 50 GB
Application data → 100 GB
```

A reasonable VM design might be:

```
VM
├── OS disk → 50 GB
└── Data disk → 100 GB
```

The disks could reside on a redundant datastore.

The Web server does not need to know whether the datastore uses RAID, ZFS, or distributed storage.

The storage backend is an infrastructure concern, while the VM consumes a logical storage resource.

---

# 41. Example: Database Storage

A database may need:

```
OS → 50 GB
Database → 500 GB
Logs → 100 GB
```

One possible architecture is:

```
VM
├── OS disk
├── Database disk
└── Log disk
```

Separating disks can make I/O patterns easier to observe and manage.

For example:

```
OS disk       → ordinary system activity
Database disk → random reads/writes
Log disk      → frequent writes
```

However, separate virtual disks do not guarantee separate physical devices. If all three reside on the same overloaded SSD, they still compete for the same underlying resources.

---

# 42. Shared Storage for a Cluster

Consider three virtualization hosts:

```
Host A
Host B
Host C
```

and one shared storage system:

```
             Shared Storage
             /      |                  /       |               Host A    Host B    Host C
```

VM disks are stored on the shared datastore.

If Host A fails, a properly configured high-availability system may restart its VMs on Host B or Host C because the VM disks remain accessible.

The shared storage is therefore part of the availability architecture.

However, if the shared storage itself has a single point of failure, the cluster still has a critical dependency.

---

# 43. Redundant Shared Storage

A production shared-storage system may require redundancy in several components:

```
Physical disks
Storage controllers
Storage network
Network switches
Power supplies
Storage nodes
```

For example:

```
Host A ───── Switch A ───── Storage Path A
           └──── Switch B ───── Storage Path B
```

The goal is to avoid a single failed cable, switch, controller, or disk taking down the entire storage service.

Redundancy should be designed systematically rather than added randomly.

---

# 44. Storage Monitoring

Storage monitoring should occur at several layers.

Inside Linux, useful commands include:

```
df -h
lsblk
df -T
iostat
```

`df -h` shows filesystem capacity. `lsblk` shows block devices and their relationships. `df -T` identifies filesystem types. `iostat` can reveal I/O activity and performance indicators.

At the virtualization layer, monitor:

```
Datastore capacity
Virtual disk consumption
Storage latency
IOPS
Throughput
Snapshot growth
Thin-provisioning consumption
```

At the physical layer, monitor:

```
Disk health
SMART status
RAID status
Controller health
Temperature
Storage-network errors
```

The purpose is correlation.

For example:

```
VM is slow
   ↓
Hypervisor shows high storage latency
   ↓
Datastore is overloaded
   ↓
Physical disks have high I/O queue
```

This provides a much stronger diagnosis than simply saying "the VM is slow."

---

# 45. Capacity Monitoring and Thin Provisioning

Suppose:

```
Datastore capacity → 2 TB
Used → 1.8 TB
Free → 200 GB
```

but thin-provisioned virtual disks have a combined logical capacity of 3 TB.

This is not immediately a problem, but it is a risk.

If the environment grows by 50 GB per day:

```
200 GB free ÷ 50 GB/day ≈ 4 days
```

The datastore could become full in approximately four days if growth continues.

This simple calculation demonstrates why storage monitoring must consider both current usage and growth rate.

---

# 46. What Happens When Storage Fills?

A full datastore can cause serious problems:

```
VM write failures
Snapshot failures
Backup failures
Guest filesystem errors
VM suspension or crashes
Management problems
```

With thin provisioning, a VM can believe that it has hundreds of gigabytes of virtual capacity remaining while the physical datastore has almost no space.

Administrators should therefore define warning and critical thresholds before storage reaches 100%.

---

# 47. Snapshot Growth

Snapshots can consume additional storage as the VM changes.

Conceptually:

```
Original Disk
     +
Changed Blocks
     ↓
Snapshot Data
```

The exact implementation differs by virtualization platform.

A long-lived snapshot on a heavily changing VM can grow substantially.

A sensible operational lifecycle is:

```
Create snapshot
      ↓
Perform change
      ↓
Verify change
      ↓
Remove snapshot
```

Snapshots should have a reason, owner, and expected lifetime.

---

# 48. Storage Migration

Virtual disks can often be migrated between storage locations:

```
Datastore A
     ↓
Virtual Disk
     ↓
Storage Migration
     ↓
Datastore B
```

This is useful when:

```
Datastore A is full
A storage system is being replaced
Performance requirements change
VMs are being reorganized
Maintenance is required
```

Some virtualization platforms support live storage migration while the VM is running.

Storage migration is one of the practical benefits of separating virtual disks from physical devices.

---

# 49. Virtual Disk Cloning

Virtual disks can often be cloned.

A template or source VM can be used to create:

```
Template
   ↓
Clone
 ┌─┼─┐
VM1 VM2 VM3
```

This is useful for laboratories and standardized server deployment.

However, cloned operating systems may contain duplicated identity information.

Administrators may need to regenerate:

```
Hostname
Network identity
SSH host keys
Machine identifiers
Application identifiers
```

A clone should therefore be prepared appropriately before production deployment.

---

# 50. Templates and Golden Images

A template is a prepared VM image used as the basis for new machines.

A typical process is:

```
Install OS
   ↓
Apply updates
   ↓
Install standard tools
   ↓
Apply security baseline
   ↓
Remove machine-specific identity
   ↓
Convert to template
   ↓
Deploy new VMs
```

Templates improve consistency and reduce deployment time.

Storage virtualization makes templates practical because the template can be represented as a virtual disk image.

---

# 51. Storage Security

A virtual disk can contain an entire operating system and sensitive application data.

Access to storage is therefore highly privileged.

An administrator with access to a VM's disk image may be able to inspect data without logging into the guest.

Storage security should consider:

```
Access control
Authentication
Encryption
Audit logs
Backup security
Management-network isolation
Secure disposal
```

Virtual storage should be treated as sensitive infrastructure data.

---

# 52. Encryption

Encryption can be applied at multiple layers:

```
Application
    ↓
Guest OS encryption
    ↓
Virtual Disk
    ↓
Storage Backend Encryption
    ↓
Physical Storage
```

Guest-level encryption can protect data from certain forms of unauthorized access to the disk contents.

Backend encryption can protect physical media if drives are removed or stolen.

Encryption creates another responsibility: key management.

If the organization loses the keys, encrypted backups or virtual disks may become unrecoverable.

---

# 53. Practical Lab 1 — Inspect a Virtual Disk

Create a Linux VM with a 20 GB virtual disk.

Inside the VM run:

```
lsblk
df -h
df -T
```

Identify:

```
The virtual disk
The partition
The filesystem
The mount point
Used capacity
Free capacity
Filesystem type
```

Then inspect the same VM from the hypervisor.

Your goal is to understand:

```
Hypervisor virtual disk
        ↓
Guest block device
        ↓
Partition
        ↓
Filesystem
        ↓
Files
```

---

# 54. Practical Lab 2 — Add a Second Virtual Disk

Add a second virtual disk:

```
Disk 1 → 20 GB → OS
Disk 2 → 10 GB → Data
```

Boot Linux and identify the new disk:

```
lsblk
```

After confirming the correct device, create a filesystem and mount it:

```
sudo mkfs.ext4 /dev/sdb
sudo mkdir /data
sudo mount /dev/sdb /data
```

Then:

```
df -h
```

Be extremely careful with `mkfs`: using it against the wrong disk can destroy existing data.

The goal is to connect:

```
Virtual disk
      ↓
Guest block device
      ↓
Filesystem
      ↓
Mount point
```

---

# 55. Practical Lab 3 — Expand a Virtual Disk

Create a VM with a 20 GB disk.

Run:

```
lsblk
df -h
```

Increase the virtual disk to 30 GB using your hypervisor.

Boot the VM and run:

```
lsblk
```

You may find:

```
Disk → 30 GB
Partition → 20 GB
Filesystem → 20 GB
```

Your task is to identify why the extra capacity is not yet visible inside the filesystem and safely extend the appropriate layers.

Document the storage stack before modifying it.

---

# 56. Practical Lab 4 — Thin Provisioning

Create a dynamically allocated virtual disk with:

```
Logical capacity → 50 GB
```

Inspect its physical consumption on the host.

Inside the VM, create a test file:

```
dd if=/dev/zero of=/data/testfile bs=1M count=1024
```

Then inspect physical storage consumption again.

The objective is to observe:

```
Logical capacity ≠ Physical consumption
```

Remove the test data afterward.

Always verify the output path before using `dd`.

---

# 57. Practical Lab 5 — Snapshot Growth

Create a test VM and take a snapshot.

Inside the VM, create and modify data repeatedly.

Observe whether the snapshot storage changes as the VM changes.

Then remove the snapshot.

The goal is to understand why long-lived snapshots can consume substantial storage and why snapshot management is part of capacity management.

---

# 58. Practical Lab 6 — Shared NFS Storage

If you have two Linux virtualization hosts, configure an NFS server.

The conceptual architecture is:

```
NFS Server
    │
    ├──────── Host A
    │
    └──────── Host B
```

Create a shared export and mount it on both hosts.

Verify that both hosts can access the same storage.

Then place a test virtual disk on the shared datastore.

The purpose is to understand why shared storage allows multiple hypervisors to access the same VM storage.

---

# 59. Practical Lab 7 — RAID Failure

If you have a safe lab environment with redundant storage, simulate the failure of one physical disk.

Observe:

```
Healthy
   ↓
Disk failure
   ↓
Degraded array
   ↓
VM continues
   ↓
Disk replacement
   ↓
Rebuild
   ↓
Healthy
```

Never perform this experiment on production hardware.

Document:

```
How failure was detected
Whether the VM remained accessible
How the array reported degradation
How the disk was replaced
How long rebuilding took
Whether performance changed during rebuild
```

This demonstrates that redundancy has an operational lifecycle.

---

# 60. Practical Lab 8 — RAID Capacity

Assume four disks of 2 TB each.

Calculate approximate usable capacity for:

```
RAID 0
RAID 1
RAID 5
RAID 6
RAID 10
```

Then compare:

```
Usable capacity
Fault tolerance
Performance
Write behavior
Rebuild implications
```

Finally answer:

> Which configuration would you choose for a database, and why?

Your answer should depend on workload requirements rather than simply naming the RAID level with the most capacity.

---

# 61. Practical Lab 9 — Storage Monitoring

Inside a Linux VM:

```
df -h
lsblk
df -T
iostat
```

At the hypervisor layer, inspect:

```
Datastore usage
Virtual disk usage
Storage latency
IOPS
Throughput
Snapshots
```

At the physical layer, inspect:

```
Disk health
RAID state
Controller status
Storage network
```

Try to correlate the observations.

For example:

```
Guest reports slow writes
        ↓
Hypervisor reports high latency
        ↓
Datastore is overloaded
        ↓
Physical storage shows high queue
```

This is the beginning of professional storage troubleshooting.

---

# 62. Troubleshooting: "No Space Left on Device"

A VM reports:

```
No space left on device
```

Do not immediately assume that the virtual disk itself is full.

Start with:

```
df -h
df -i
lsblk
```

The filesystem could be full.

The filesystem could have free capacity but no free inodes.

The partition could be smaller than the virtual disk.

The datastore could be full.

The physical storage could be exhausted.

Therefore investigate:

```
Filesystem capacity
Inodes
Partition size
Virtual disk capacity
Datastore capacity
Physical capacity
```

This is why understanding the storage stack is more valuable than memorizing one troubleshooting command.

---

# 63. Troubleshooting: Slow VM Storage

A database VM becomes slow.

You observe:

```
CPU → 30%
RAM → 50%
```

It is tempting to conclude that the VM is healthy.

But suppose storage latency is very high.

Follow the path:

```
Database
   ↓
Guest filesystem
   ↓
Virtual disk
   ↓
Datastore
   ↓
Storage network
   ↓
Storage backend
   ↓
Physical disks
```

Possible causes include:

```
Datastore overload
High I/O queue
RAID rebuild
Failed disk
Network congestion
Storage controller issue
Thin-provisioning pressure
```

The correct response is to identify the bottleneck rather than blindly adding CPU or RAM.

---

# 64. Troubleshooting: Full Datastore

Suppose:

```
Datastore → 1 TB
Used → 950 GB
Free → 50 GB
```

and many VMs use thin provisioning.

An administrator creates another 200 GB virtual disk.

The VM may see:

```
200 GB available
```

while the datastore physically has only 50 GB free.

This is dangerous.

The administrator must distinguish:

```
Logical capacity
Actual physical consumption
Growth rate
```

Thin provisioning is useful only when physical capacity is actively managed.

---

# 65. Troubleshooting: RAID Degraded

A RAID 5 array loses one disk but the VM continues running.

An inexperienced administrator may think:

> "Everything is fine because the VM still works."

The array is actually degraded.

The correct lifecycle is:

```
Healthy
   ↓
Disk failure
   ↓
Degraded
   ↓
Replace failed disk
   ↓
Rebuild
   ↓
Healthy
```

During degradation, the storage system has reduced protection. Another failure before or during recovery can have much more serious consequences.

---

# 66. Troubleshooting: Shared Storage Failure

Several VMs become unresponsive simultaneously, while the hypervisor hosts remain reachable.

You discover:

```
CPU → normal
RAM → normal
Storage network → failure
```

If all VM disks reside on shared storage, the storage network can be the common dependency.

The architecture is:

```
Many VMs
    ↓
Shared Datastore
    ↓
Storage Network
```

A failure in that shared path can therefore affect many independent VMs at once.

This is why shared storage networks require careful redundancy and monitoring.

---

# 67. Storage Design Principles

Several principles should guide storage architecture.

**First, separate capacity from performance.** A larger disk is not necessarily a faster disk.

**Second, understand the complete storage stack.** Always know how guest storage maps to the physical backend.

**Third, monitor thin provisioning.** Logical capacity can exceed physical capacity.

**Fourth, remember that redundancy is not backup.** RAID protects against specific hardware failures; backups provide historical recovery.

**Fifth, design around failure domains.** Two copies on one physical device are not independent protection.

**Sixth, test recovery.** A storage architecture is only as useful as your ability to recover from failure.

---

# 68. Example Architecture: Small Virtualization Cluster

A small cluster might look like:

```
                  Virtualization Cluster

        ┌───────────────────────────────────┐
        │                                   │
     Host A                              Host B
        │                                   │
        └─────────────────┬─────────────────┘
                          │
                   Storage Network
                          │
                          ▼
                    Shared Storage
                          │
                    ┌─────┴─────┐
                    │           │
                  Disk 1      Disk 2
                    │           │
                    └─────┬─────┘
                          │
                    RAID / Pool
```

VMs see virtual disks.

The hypervisors see shared storage.

The storage platform sees physical disks.

Each layer has different responsibilities and different failure modes.

---

# 69. Example Architecture: Distributed Storage

Another architecture can combine local disks across several nodes:

```
Node A             Node B             Node C
Local disks        Local disks        Local disks
    │                  │                  │
    └──────────────────┼──────────────────┘
                       ↓
              Distributed Storage
                       ↓
                  VM Storage
```

Data can be replicated across nodes.

This can avoid dependence on a single centralized storage appliance.

However, distributed storage requires careful planning for:

```
Network capacity
Latency
Replication
Consistency
Node failure
Disk failure
Recovery
Monitoring
```

It is powerful but operationally more complex.

---

# 70. Backup and Storage Redundancy

A mature storage design combines several protections:

```
VM
 ↓
Redundant Storage
 ↓
Backup System
 ↓
Separate Backup Repository
 ↓
Off-site / Isolated Copy
```

Each layer solves a different problem.

Redundancy improves availability after certain hardware failures.

Backups provide historical recovery.

Off-site or isolated copies protect against larger events such as ransomware, accidental deletion, and site disasters.

No single storage technology should be expected to solve every failure scenario.

---

# 71. Final Synthesis

Storage virtualization separates the logical storage consumed by a VM from the physical storage that ultimately holds the data.

A VM sees:

```
Virtual Disk
```

but underneath it may be:

```
File
Logical Volume
ZFS volume
RAID array
SAN LUN
NFS-backed storage
Distributed storage
```

Virtual disk configuration includes capacity, provisioning method, disk format, controller, performance characteristics, and placement.

Thin provisioning can improve utilization because physical storage grows with actual use, but it requires careful capacity monitoring.

Shared storage allows multiple virtualization hosts to access the same VM storage. This is useful for migration, clustering, and high availability.

Shared storage can use NFS, iSCSI, Fibre Channel, or distributed storage such as Ceph.

Storage redundancy protects against specific hardware failures. RAID 1, RAID 5, RAID 6, and RAID 10 offer different combinations of capacity efficiency, performance, and fault tolerance.

But RAID is not backup.

A redundant array can still lose data through:

```
Accidental deletion
Corruption
Ransomware
Application failure
Administrator error
Fire
Flood
Theft
Site disaster
```

Therefore, reliable virtualized storage combines:

```
Capacity
    +
Performance
    +
Redundancy
    +
Backup
    +
Recovery Testing
```

The complete storage lifecycle is:

```
Plan
  ↓
Create Storage
  ↓
Allocate Virtual Disks
  ↓
Deploy VM
  ↓
Monitor Capacity
  ↓
Monitor Performance
  ↓
Maintain Redundancy
  ↓
Backup
  ↓
Test Recovery
  ↓
Expand / Migrate
  ↓
Retire Securely
```

---

# 72. Essential Knowledge Before Moving On

Before continuing to the next chapter, you should be able to explain the difference between physical and virtual disks.

You should understand:

```
Guest filesystem
      ↓
Virtual disk
      ↓
Datastore / storage pool
      ↓
Physical storage
```

You should understand thin and thick provisioning and why thin provisioning requires monitoring.

You should know why expanding a virtual disk may require additional operations inside the guest.

You should understand the purpose of shared storage and its importance for virtualization clusters.

You should understand the conceptual difference between file-based and block-based shared storage.

You should be able to explain the basic characteristics of RAID 0, RAID 1, RAID 5, RAID 6, and RAID 10.

Most importantly, you should distinguish:

```
Redundancy
    ↓
Keeps storage available after certain hardware failures

Backup
    ↓
Provides recoverable historical copies

Disaster Recovery
    ↓
Restores service after major infrastructure failure
```

The key mental model for this chapter is:

```
Application
      ↓
Guest Filesystem
      ↓
Virtual Block Device
      ↓
Virtual Disk
      ↓
Hypervisor Storage Layer
      ↓
Datastore / Storage Pool
      ↓
RAID / ZFS / SAN / NAS / Distributed Storage
      ↓
Physical Disks
```

When a virtual machine has a storage problem, do not stop at the guest operating system. Trace the complete chain. That habit will become one of the most important skills in virtualization and systems administration.