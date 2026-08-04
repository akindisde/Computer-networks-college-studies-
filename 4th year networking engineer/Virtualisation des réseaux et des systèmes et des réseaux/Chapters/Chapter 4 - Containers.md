# Chapter 4 — Containers

## Introduction

Containers are one of the most important technologies in modern systems administration, cloud computing, DevOps, and virtualization. They provide a way to isolate applications and their dependencies while allowing multiple isolated environments to share the same operating-system kernel.

Containers are often described as "lightweight virtualization," but this description can be misleading if it makes us think that a container is simply a smaller virtual machine. A container and a virtual machine use fundamentally different isolation mechanisms.

A traditional virtual machine virtualizes hardware. Each virtual machine normally contains its own operating-system kernel. A container instead isolates processes at the operating-system level. Containers running on the same Linux host normally share the host's Linux kernel while having separate process, filesystem, network, and resource views.

This distinction has major consequences for performance, startup time, resource consumption, security boundaries, portability, and administration.

The purpose of this chapter is to build a strong conceptual foundation before moving into more advanced container administration. We will first understand what a container actually is, then compare containers with traditional virtual machines, and finally study four important technologies: LXC, LXD, Docker, and Podman.

---

# 1. Learning Objectives

By the end of this chapter, you should be able to explain what a container is and describe the operating-system mechanisms that make container isolation possible.

You should understand why containers are generally lighter than full virtual machines and why containers are not equivalent to virtual machines.

You should be able to explain the role of Linux namespaces, cgroups, capabilities, filesystem isolation, and container images at a conceptual level.

You should understand the distinction between a container image and a running container.

You should also understand the architectural differences between LXC, LXD, Docker, and Podman and recognize which problems each technology is designed to solve.

Finally, you should be able to create and manage basic containers in a laboratory environment, inspect their resources and networking, persist data outside the container lifecycle, and reason about the security and operational implications of containerization.

---

# 2. What Is a Container?

A container is an isolated execution environment for processes.

The simplest mental model is:

```
Physical Server
      ↓
Linux Kernel
      ↓
Container Runtime
      ↓
┌──────────┬──────────┬──────────┐
│Container │Container │Container │
│    A     │    B     │    C     │
└──────────┴──────────┴──────────┘
```

All three containers may run different applications, have different filesystems, different process trees, different network configurations, and different resource limits.

However, they normally use the same underlying kernel.

For example, a host could run:

```
Container A → Nginx
Container B → PostgreSQL
Container C → Redis
```

The applications behave as though they have their own environments, but the host kernel is responsible for executing their processes.

This is fundamentally different from:

```
Physical Server
      ↓
Hypervisor
      ↓
┌──────────┬──────────┬──────────┐
│ VM A     │ VM B     │ VM C     │
│ OS+Kernel│ OS+Kernel│ OS+Kernel│
└──────────┴──────────┴──────────┘
```

In the VM model, each guest operating system has its own kernel.

---

# 3. Containerization Versus Virtualization

The central difference can be expressed simply.

Traditional virtualization creates virtual hardware:

```
Physical Hardware
       ↓
Hypervisor
       ↓
Virtual Hardware
       ↓
Guest Operating System
       ↓
Application
```

Containerization creates isolated operating-system environments:

```
Physical Hardware
       ↓
Host Operating System
       ↓
Container Runtime
       ↓
Isolated Processes
       ↓
Application
```

The virtual machine abstracts hardware.

The container abstracts the process environment.

This difference explains why containers can usually start much faster and consume fewer resources than full virtual machines.

---

# 4. Why Containers Are Lightweight

Suppose a physical server has:

```
32 GB RAM
8 CPU cores
```

Imagine running three conventional virtual machines:

```
VM 1 → Linux kernel + services
VM 2 → Linux kernel + services
VM 3 → Linux kernel + services
```

Each VM has its own operating system, kernel, system processes, libraries, and services.

With containers:

```
Host Linux Kernel
      │
      ├── Container A
      ├── Container B
      └── Container C
```

The containers do not each need to boot a separate Linux kernel.

They can therefore avoid significant duplicated operating-system overhead.

This does not mean containers consume no resources. Each container still consumes CPU, memory, storage, network resources, and other kernel resources. The difference is that the operating-system foundation is shared.

---

# 5. Container Startup

A virtual machine normally performs a boot process:

```
VM Start
   ↓
Virtual firmware
   ↓
Bootloader
   ↓
Kernel
   ↓
Init system
   ↓
System services
   ↓
Application
```

A container usually starts a process directly inside an isolated environment:

```
Container Start
      ↓
Create namespaces
      ↓
Apply resource controls
      ↓
Prepare filesystem
      ↓
Start application process
```

This is one reason containers can start extremely quickly.

The container is not normally booting an independent kernel.

---

# 6. Containers Are Process Isolation

A very useful way to think about containers is:

> A container is primarily a group of processes isolated from other processes.

Suppose the host has:

```
PID 1 → systemd
PID 2000 → sshd
PID 3000 → container process
```

Inside a PID namespace, the container may see:

```
PID 1 → container application
```

The same process can therefore have different process identifiers depending on which namespace is observing it.

This is a key concept.

The container does not create another physical computer. It creates an isolated process environment.

---

# 7. Linux Namespaces

Linux namespaces are one of the fundamental mechanisms behind containers.

A namespace controls what a process can see about a particular category of system resources.

Important namespace types include:

```
PID
Network
Mount
UTS
IPC
User
Cgroup
```

The exact implementation and available namespace types depend on the Linux kernel and runtime.

The key idea is that processes can be given different views of the same underlying system.

---

# 8. PID Namespaces

A PID namespace isolates process identifiers.

Imagine the host sees:

```
PID 5000 → nginx
PID 5001 → worker
```

Inside the container, the application may see:

```
PID 1 → nginx
PID 2 → worker
```

The host can still see the container processes because the host operates outside the container's PID namespace.

This creates a hierarchical relationship:

```
Host Process Namespace
        │
        └── Container PID Namespace
                 │
                 ├── PID 1
                 └── PID 2
```

This isolation is why a process inside one ordinary container cannot simply list every host process as though it were running directly on the host.

---

# 9. Network Namespaces

Network namespaces provide isolated network environments.

A container can have its own:

```
Network interfaces
IP addresses
Routing table
ARP/neighbor information
Network namespaces
```

A simplified architecture is:

```
Host
 │
 ├── eth0
 │
 └── Virtual Ethernet Pair
          │
          └── Container
                │
                └── eth0
```

The container can therefore have an address such as:

```
172.17.0.2
```

while the host has:

```
192.168.1.10
```

The container's network configuration can be isolated from the host's network namespace.

---

# 10. Mount Namespaces

Mount namespaces provide an isolated view of mounted filesystems.

For example, the host might have:

```
/
├── /etc
├── /home
├── /var
└── /srv
```

A container may instead see a filesystem constructed specifically for it:

```
/
├── /etc
├── /usr
├── /var
└── /app
```

The container sees a root filesystem that is different from the host's root filesystem.

This is essential for making the container appear to have its own operating-system filesystem.

---

# 11. UTS Namespaces

UTS namespaces isolate system identity information such as the hostname.

The host could have:

```
hostname → virtualization-host
```

while the container sees:

```
hostname → web01
```

This helps applications behave as though they are running on a separate system.

Again, this is an isolated view rather than a separate physical computer.

---

# 12. IPC Namespaces

IPC namespaces isolate certain forms of inter-process communication.

Processes can use mechanisms such as:

```
Shared memory
Message queues
Semaphores
```

Containers can be isolated so that processes in one container cannot freely interact with IPC objects belonging to another isolated environment.

This is another component of process isolation.

---

# 13. User Namespaces

User namespaces allow processes to have different user and group identifiers inside and outside a namespace.

This is particularly important for rootless containers.

For example, a process can appear as:

```
Inside container:
UID 0 → root
```

while being mapped to an unprivileged user identity on the host.

Conceptually:

```
Container UID 0
      ↓
User namespace mapping
      ↓
Host unprivileged UID
```

This is one of the mechanisms that allows some container technologies to provide rootless operation.

---

# 14. Control Groups

Namespaces provide isolation.

Control groups, usually called cgroups, provide resource control and accounting.

A cgroup can control or account for resources such as:

```
CPU
Memory
PIDs
Block I/O
```

Imagine a host with:

```
16 CPU cores
64 GB RAM
```

A container might be configured with:

```
CPU → 2 cores worth of CPU time
RAM → 4 GB maximum
```

Conceptually:

```
Host
 │
 ├── Container A
 │     ├── CPU limit
 │     └── RAM limit
 │
 ├── Container B
 │     ├── CPU limit
 │     └── RAM limit
 │
 └── Container C
```

Cgroups are therefore important for predictable resource management.

---

# 15. Namespaces Versus Cgroups

A common beginner mistake is to treat namespaces and cgroups as the same thing.

They solve different problems.

Namespaces primarily answer:

> What can this process see?

Cgroups primarily answer:

> How much of the resource can this process use, and how should it be accounted for?

For example:

```
Namespace → Container sees only its own process tree.

Cgroup → Container cannot consume more than its configured memory limit.
```

A modern container runtime combines several kernel mechanisms to create the complete container environment.

---

# 16. Container Filesystems

A container needs a filesystem.

For example:

```
/
├── bin
├── etc
├── home
├── lib
├── usr
└── var
```

This filesystem can be constructed from an image or another storage mechanism.

The container's filesystem is generally isolated from the host.

The container may write files during its lifetime, but those writes need to be managed carefully because the container lifecycle and application-data lifecycle are not always the same.

This leads to an important principle:

> Application data should generally be separated from the disposable container environment.

---

# 17. Images and Containers

A container image is not the same thing as a running container.

Think of an image as a template:

```
Container Image
      ↓
Create
      ↓
Running Container
```

For example:

```
Ubuntu image
      ↓
Container A
Container B
Container C
```

The same image can be used to create multiple containers.

A container is an instantiated execution environment based on an image or root filesystem.

This distinction becomes especially important with Docker and Podman.

---

# 18. Image Layers

Many container image systems use layers.

Conceptually:

```
Base OS layer
      ↓
Application dependencies
      ↓
Application files
      ↓
Configuration
```

For example:

```
Layer 1 → Ubuntu base
Layer 2 → Python runtime
Layer 3 → Python dependencies
Layer 4 → Application
```

Multiple images can share common layers.

This reduces duplicated storage and makes image distribution more efficient.

---

# 19. Copy-on-Write

Container storage systems often use copy-on-write techniques.

A simplified model is:

```
Image Layers
     │
     ▼
Container Writable Layer
```

The image remains a reusable base.

When the container modifies a file, the storage system can create or update data in a writable layer instead of changing the original image.

This is one reason images can be reused to create many containers.

---

# 20. Ephemeral Containers

Containers are often designed to be disposable.

For example:

```
Create container
      ↓
Run application
      ↓
Application exits
      ↓
Remove container
```

This is a major difference in operational philosophy from traditional servers.

Instead of manually repairing a container, a common pattern is:

```
Replace
rather than
repair
```

If the application container becomes corrupted, the desired state can often be recreated from the image and persistent data.

This is one of the ideas behind immutable infrastructure.

---

# 21. Persistent Data

A major challenge is deciding what data should survive container deletion.

Suppose:

```
PostgreSQL container
      ↓
Database files
```

If the database files exist only inside the container's writable layer and the container is deleted, the data may be lost.

A better architecture separates application code from persistent data:

```
Container
   │
   └── Application

Persistent Volume
   │
   └── Database data
```

The container can be replaced while the data remains.

This principle is fundamental to practical container administration.

---

# 22. Volumes and Bind Mounts

Container technologies commonly provide mechanisms to expose persistent storage.

A **volume** is storage managed by the container platform.

A **bind mount** exposes an existing host filesystem path inside the container.

Conceptually:

```
Host directory
      │
      └── Bind mount
              ↓
          Container
```

or:

```
Container
     ↓
Managed volume
     ↓
Host storage backend
```

The exact terminology and commands differ between Docker, Podman, LXC, and LXD.

---

# 23. Containers and Networking

Containers can communicate in several ways.

A simplified model is:

```
Container A
    │
    ├── Virtual interface
    │
    └── Container network
              │
              ├── Container B
              └── Host
```

The runtime may create virtual Ethernet interfaces, bridges, routing rules, NAT, firewall rules, or other networking components.

For example:

```
Container
172.17.0.2
     │
     ↓
Virtual Bridge
     │
     ↓
Host
192.168.1.10
     │
     ↓
Internet
```

The container may use private addressing and have its traffic translated through the host.

---

# 24. Container Port Publishing

Suppose an Nginx container listens on:

```
Container port → 80
```

The host can publish it on:

```
Host port → 8080
```

Conceptually:

```
Client
  ↓
Host:8080
  ↓
Container:80
  ↓
Nginx
```

This is a common container networking pattern.

It allows several containers to listen on the same internal port while exposing different host ports.

---

# 25. Containers and DNS

Container environments often provide internal DNS mechanisms.

For example:

```
web
 │
 └── database
```

The web application may connect to:

```
database:5432
```

instead of using a fixed IP address.

This is particularly useful in dynamic environments because containers can be recreated and receive different IP addresses.

Service discovery therefore becomes more important as containerized architectures grow.

---

# 26. Virtual Machines and Containers Compared

A simplified comparison is:

|Characteristic|Virtual Machine|Container|
|---|---|---|
|Kernel|Usually independent|Shared host kernel|
|Isolation level|Hardware/VM boundary|OS/process boundary|
|Startup|Usually slower|Usually faster|
|Resource overhead|Higher|Generally lower|
|Guest OS|Full OS|User-space environment|
|Portability|High|High, but kernel-dependent|
|Typical unit|VM/server|Application/process|
|Common use|Full OS isolation|Application deployment|

These are generalizations. Modern virtualization and container platforms have many advanced features, and actual performance depends on implementation and workload.

---

# 27. Heavy Virtualization

Heavy virtualization, often called full virtualization in this context, creates virtual machines.

The architecture is:

```
Hardware
   ↓
Hypervisor
   ↓
Virtual Hardware
   ↓
Guest Kernel
   ↓
Guest User Space
   ↓
Application
```

A VM can run an operating system with a kernel different from the host kernel.

For example:

```
Host → Linux
VM   → Windows
```

This is possible because the VM receives virtualized hardware.

---

# 28. Container Virtualization

The container architecture is:

```
Hardware
   ↓
Host Kernel
   ↓
Container Runtime
   ↓
Namespaces + cgroups
   ↓
Container User Space
   ↓
Application
```

The container normally needs a compatible host kernel.

For example, Linux containers rely on Linux kernel functionality.

A Linux container does not contain a completely independent Linux kernel merely because its filesystem contains directories such as:

```
/bin
/etc
/usr
/lib
```

Those files represent user space. The kernel remains the host kernel.

---

# 29. Important Consequence: Kernel Compatibility

Suppose a container image contains:

```
Ubuntu user space
```

The container does not automatically include:

```
Ubuntu kernel
```

It uses the host kernel.

This means containers have stronger dependencies on the host kernel than traditional virtual machines.

A VM can usually run:

```
Host Linux
      ↓
VM Windows
```

A Linux container cannot simply provide a Windows kernel through its image.

If you need a different kernel family, a VM is often the appropriate isolation boundary.

---

# 30. Virtual Machines and Containers Can Be Combined

Containers and VMs are not competing technologies in every architecture.

A common architecture is:

```
Physical Hardware
       ↓
Hypervisor
       ↓
Linux VM
       ↓
Container Runtime
       ↓
Containers
```

For example:

```
Physical Server
     ↓
Proxmox / KVM
     ↓
Linux VM
     ↓
Docker / Podman
     ↓
Application Containers
```

This provides VM-level isolation around the operating system and container-level isolation inside the VM.

Cloud platforms commonly use combinations of these techniques.

---

# 31. When Should You Use a VM?

A virtual machine is often appropriate when you need:

```
A different kernel
Strong OS-level isolation
A complete operating system environment
Windows and Linux coexistence
Kernel-level experimentation
Traditional server workloads
```

For example, if a workload requires Windows Server, a Linux container is not a substitute for a Windows VM.

Similarly, kernel development and operating-system experimentation generally belong in VMs rather than ordinary containers.

---

# 32. When Should You Use Containers?

Containers are particularly useful when you need:

```
Fast application deployment
Reproducible environments
Application isolation
Microservices
CI/CD workloads
Development environments
Scalable application instances
Efficient resource usage
```

A Web application can be packaged with its dependencies and deployed consistently across multiple environments.

This reduces the classic problem:

> "It works on my machine."

The goal is to make the application environment reproducible.

---

# 33. LXC

LXC stands for Linux Containers.

LXC is a Linux container technology focused on system containers.

A system container is designed to resemble a lightweight Linux system rather than merely executing one isolated application process.

For example:

```
LXC Container
├── init
├── sshd
├── cron
├── shell
├── networking
└── application services
```

This makes LXC conceptually closer to a lightweight virtual server than the typical Docker application-container model.

LXC uses Linux kernel primitives such as namespaces and cgroups to provide isolation.

---

# 34. LXC and System Containers

An LXC container can behave like a small Linux machine.

You might enter it and see:

```
root@container:~#
```

You can install packages, configure services, create users, and manage files.

For example:

```
Container
   ↓
Ubuntu user space
   ↓
systemd
   ↓
SSH
   ↓
Web service
```

The container can therefore be useful for environments where you want an operating-system-like experience without the overhead of a full VM.

---

# 35. LXC Architecture

A simplified LXC architecture is:

```
Linux Host
    │
    ├── Linux Kernel
    │
    └── LXC
         │
         ├── Container A
         ├── Container B
         └── Container C
```

LXC interacts closely with Linux kernel isolation mechanisms.

It is therefore useful for learning the relationship between:

```
Linux kernel
Namespaces
Cgroups
Filesystem isolation
Networking
Container processes
```

---

# 36. LXD

LXD is a container management system built around the concept of system containers and virtual machines.

It provides a higher-level management experience than directly manipulating low-level LXC primitives.

Conceptually:

```
Administrator
      ↓
LXD
      ↓
Containers / VMs
      ↓
Linux kernel / virtualization
```

LXD provides features for managing images, storage, networking, profiles, instances, and remote environments.

This makes it useful for administrators who want infrastructure-style management of Linux containers.

---

# 37. LXC Versus LXD

A useful conceptual distinction is:

```
LXC
 ↓
Container technology / low-level interface

LXD
 ↓
Higher-level management platform
```

LXC provides the underlying container functionality.

LXD provides management abstractions and operational features around system containers and, in modern versions, virtual machines as well.

You should not think of LXD as simply a different name for LXC.

---

# 38. Docker

Docker popularized the application-container model and strongly influenced modern container workflows.

A Docker container typically represents an application or service:

```
Container
   ↓
Application
   ↓
Process
```

For example:

```
Nginx container
PostgreSQL container
Redis container
Python application container
```

Rather than building a container as a complete virtual server with many services, Docker encourages a model where each container has a focused responsibility.

---

# 39. Docker's Application-Centric Model

A common Docker architecture is:

```
Web Application
      ↓
Web Container

Database
      ↓
Database Container

Cache
      ↓
Redis Container
```

The application may consist of several cooperating containers.

For example:

```
                 Client
                    │
                    ▼
                Web Proxy
                    │
                    ▼
              Application
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
      PostgreSQL            Redis
```

Each component can be independently deployed and updated.

This architecture is strongly associated with microservices and cloud-native development.

---

# 40. Docker Images

Docker uses images as the basis for containers.

For example:

```
Image
ubuntu:24.04
```

can be used to create a container.

An image may contain:

```
Base filesystem
Application binaries
Libraries
Configuration
Metadata
```

The image is normally immutable from the user's perspective.

When a container starts, a writable layer can be added above the image layers.

---

# 41. Dockerfiles

A Dockerfile describes how an image can be built.

A simplified example is:

```
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

The important idea is that the application environment becomes declarative.

Instead of manually documenting:

```
Install Python
Install package A
Install package B
Copy files
Configure startup
```

the Dockerfile can encode the build process.

This improves reproducibility.

---

# 42. Docker Image Build Process

A simplified workflow is:

```
Dockerfile
    ↓
docker build
    ↓
Image
    ↓
docker run
    ↓
Container
```

For example:

```
docker build -t myapp .
```

Then:

```
docker run myapp
```

The image becomes a reusable artifact.

The same image can be deployed on different compatible hosts running a container runtime.

---

# 43. Container Registries

Images need to be stored and distributed.

A container registry provides a repository for images.

Conceptually:

```
Developer
    ↓
Build Image
    ↓
Registry
    ↓
Server
    ↓
Pull Image
    ↓
Run Container
```

A registry can store versions such as:

```
myapp:1.0
myapp:1.1
myapp:2.0
```

Public and private registries are both common.

In production environments, image provenance, authentication, scanning, and access control become important.

---

# 44. Docker Networking

Docker can create networks for containers.

A simple architecture is:

```
Container A ─┐
             │
Container B ─┼── Docker Network
             │
Container C ─┘
```

Containers attached to the same network can communicate according to the network's configuration.

Docker also provides port publishing.

For example:

```
docker run -p 8080:80 nginx
```

This conceptually maps:

```
Host port 8080
       ↓
Container port 80
```

---

# 45. Docker Volumes

Persistent data can be stored using volumes.

For example:

```
docker volume create dbdata
```

Then a database container can use that volume.

Conceptually:

```
PostgreSQL Container
        │
        ▼
     dbdata
        │
        ▼
Persistent Host Storage
```

The container can be removed and recreated while the database data remains in the volume.

This is a central principle of stateful container deployment.

---

# 46. Docker Compose

Real applications often contain multiple services.

Instead of starting every container manually, a declarative configuration can describe the application stack.

For example:

```
Compose Application
├── web
├── app
├── database
└── cache
```

A Compose configuration can define:

```
Services
Networks
Volumes
Environment variables
Ports
Dependencies
```

The advantage is reproducibility.

An administrator can describe the desired application architecture and recreate it consistently.

---

# 47. Podman

Podman is another container engine with a strong focus on compatibility with OCI container standards and daemonless operation.

A simplified model is:

```
User
 ↓
Podman
 ↓
Container
```

Unlike the traditional Docker daemon model, Podman does not require a single central daemon to manage all containers.

Podman can also support rootless containers, allowing ordinary users to run containers without giving the container engine full root privileges.

---

# 48. Rootless Containers

Rootless containers are an important security concept.

Traditional administration might look like:

```
User
  ↓
Root
  ↓
Container Runtime
  ↓
Container
```

A rootless model can look more like:

```
Unprivileged User
       ↓
User Namespace
       ↓
Container
```

Inside the container, the user may appear to be root:

```
Container → UID 0
```

while the host treats the process as belonging to an unprivileged user.

This reduces the consequences of some classes of container compromise.

Rootless does not mean "automatically secure." Networking, storage, kernel vulnerabilities, capabilities, and configuration still matter.

---

# 49. Docker Versus Podman

Docker and Podman both provide modern container workflows, but their architectures differ.

Docker traditionally uses:

```
Docker CLI
    ↓
Docker Daemon
    ↓
Container Runtime
    ↓
Containers
```

Podman uses a daemonless model:

```
Podman CLI
    ↓
Container Runtime
    ↓
Containers
```

Podman also emphasizes rootless operation and uses open container standards.

From a command-line perspective, the tools are similar enough that administrators familiar with Docker can often understand Podman quickly.

However, operational details such as networking, service management, rootless storage, and orchestration should not be assumed to be identical.

---

# 50. LXC/LXD Versus Docker/Podman

A useful high-level comparison is:

|   |   |   |
|---|---|---|
|Technology|Primary Model|Typical Focus|
|LXC|System containers|Lightweight Linux systems|
|LXD|Managed system containers + VMs|Infrastructure-style management|
|Docker|Application containers|Application packaging/deployment|
|Podman|Application containers|Daemonless/rootless workflows|

This table is a starting point, not a rigid rule.

Modern tools overlap. LXD supports virtual machines, Docker can run many kinds of workloads, and Podman can manage pods and system-oriented use cases.

The most useful distinction is the operational model.

---

# 51. System Containers Versus Application Containers

A system container aims to behave more like a complete operating-system environment:

```
Container
├── init
├── services
├── users
├── SSH
└── applications
```

An application container is usually centered around one main application or service:

```
Container
└── Main application process
```

LXC/LXD are strongly associated with system containers.

Docker and Podman are strongly associated with application containers.

This distinction helps explain why their workflows and tooling differ.

---

# 52. One Process Per Container: A Principle, Not a Law

You will often hear:

> "One process per container."

This is a useful architectural guideline, especially for application containers, but it is not a strict technical requirement.

A container can run multiple processes.

For example:

```
Container
├── nginx
├── application
└── helper process
```

However, multiple independent services inside one application container can complicate:

```
Logging
Monitoring
Restart behavior
Scaling
Failure isolation
Lifecycle management
```

For modern application architectures, separating independently managed services into separate containers is often cleaner.

---

# 53. Container Security Model

Container isolation is not identical to VM isolation.

A container shares the host kernel.

Therefore:

```
Container
      ↓
Shared Kernel
      ↓
Host
```

A kernel vulnerability that allows a process to escape its container can potentially affect the host and other workloads.

This means container security requires:

```
Kernel security
Minimal privileges
Capabilities management
Seccomp
AppArmor / SELinux
User namespaces
Read-only filesystems where appropriate
Image security
Network controls
Resource limits
```

Container security is a layered discipline.

---

# 54. Linux Capabilities

Traditional Unix privilege is often associated with root.

Linux capabilities divide some privileged operations into separate capability sets.

A container can therefore run without receiving every possible root privilege.

Examples include capabilities related to:

```
Network administration
Mounting
Changing system time
Loading kernel modules
Changing process credentials
```

The principle is:

> Give the container only the privileges it actually needs.

This is the principle of least privilege.

---

# 55. Seccomp

Seccomp, or secure computing mode, can restrict system calls available to processes.

A container application normally does not need every Linux system call.

A seccomp profile can therefore reduce the available attack surface.

Conceptually:

```
Application
    ↓
System Calls
    ↓
Seccomp Filter
    ↓
Kernel
```

If an application attempts an unwanted system call, the kernel can block it according to the configured policy.

---

# 56. AppArmor and SELinux

Linux security frameworks such as AppArmor and SELinux can add mandatory access-control policies.

Instead of relying only on traditional Unix permissions:

```
User
Group
Mode bits
```

the system can enforce additional rules describing what a process is permitted to access.

For containers, this can provide an additional security boundary.

A strong security architecture therefore combines:

```
Namespaces
+
Cgroups
+
Capabilities
+
Seccomp
+
MAC policies
+
Filesystem permissions
```

No single mechanism should be considered sufficient on its own.

---

# 57. Container Image Security

Container security begins before the container starts.

If the image contains vulnerable software:

```
Vulnerable Image
      ↓
Container
      ↓
Vulnerable Application
```

Container isolation does not automatically fix vulnerable applications.

Image security practices include:

```
Use trusted base images
Keep dependencies updated
Scan images
Minimize installed packages
Avoid secrets in images
Pin important dependencies
Verify provenance
```

An image should be treated as a software artifact that must go through a security lifecycle.

---

# 58. Secrets

A common mistake is placing passwords directly in a container image.

For example, this is dangerous conceptually:

```
Dockerfile
   ↓
DATABASE_PASSWORD=secret123
```

The secret may become embedded in an image layer or source repository.

Secrets should instead be injected at runtime using appropriate secret-management mechanisms.

The general principle is:

```
Image → Application code and dependencies

Runtime configuration → Environment-specific secrets
```

Production systems may use dedicated secret-management systems.

---

# 59. Resource Limits

Without resource controls, one container can consume excessive resources.

Suppose:

```
Container A → memory leak
Container B → normal application
Container C → database
```

If Container A consumes all available memory, the host can become unstable.

Cgroups allow resource limits to be applied.

For example:

```
Container A
Memory limit → 512 MB

Container B
Memory limit → 2 GB

Container C
Memory limit → 8 GB
```

Resource limits improve isolation and predictability.

---

# 60. CPU Limits

CPU can be controlled in different ways.

A container can receive a CPU quota or a relative CPU weight.

For example:

```
Web container
CPU limit → 2 CPU cores

Background worker
CPU limit → 1 CPU core
```

The exact configuration depends on the container runtime.

The key concept is that container resource control is not just about limiting memory. CPU, process count, storage I/O, and other resources may also need to be controlled.

---

# 61. Container Lifecycle

A typical application-container lifecycle is:

```
Image
  ↓
Create
  ↓
Start
  ↓
Running
  ↓
Stop
  ↓
Restart / Remove
```

A container can be recreated from the same image.

This makes container lifecycle management different from traditional server administration.

The container itself is often disposable.

The important persistent objects are usually:

```
Images
Volumes
Configuration
Secrets
Networks
Application data
```

---

# 62. Docker Basic Lifecycle Example

A simple Docker workflow might look like:

```
docker pull nginx
docker run -d --name web -p 8080:80 nginx
docker ps
docker logs web
docker stop web
docker rm web
```

The sequence means:

```
Pull image
    ↓
Create and start container
    ↓
Inspect running container
    ↓
Inspect logs
    ↓
Stop container
    ↓
Remove container
```

The container can then be recreated from the image.

---

# 63. Inspecting Containers

When administering containers, learn to inspect them rather than guessing.

With Docker:

```
docker ps
docker inspect web
docker logs web
docker stats
```

These provide information about:

```
Container state
Configuration
Network
Mounts
Resources
Logs
```

Podman provides similar concepts:

```
podman ps
podman inspect web
podman logs web
podman stats
```

The exact command syntax may vary between versions and configurations.

---

# 64. Entering a Running Container

Sometimes you need an interactive shell.

For Docker:

```
docker exec -it web /bin/sh
```

The exact shell may be:

```
/bin/sh
```

or:

```
/bin/bash
```

depending on the image.

This allows you to inspect:

```
Processes
Files
Environment
Network
Configuration
```

However, interactive debugging should not replace proper logging and monitoring.

A production container should ideally expose useful logs and health information without requiring administrators to enter it manually.

---

# 65. Container Logging

Containers often write application output to standard streams:

```
stdout
stderr
```

The runtime can collect these logs.

For example:

```
docker logs web
```

This supports centralized log management.

A scalable architecture may look like:

```
Container
   ↓
stdout/stderr
   ↓
Container Runtime
   ↓
Log Collector
   ↓
Central Logging System
```

This is more scalable than storing arbitrary log files inside disposable containers.

---

# 66. Health Checks

A process being alive does not necessarily mean the application is healthy.

For example:

```
Nginx process → running
```

but:

```
Application backend → unavailable
Database → unreachable
```

A health check can test actual application functionality.

Conceptually:

```
Container
   ↓
Health Check
   ↓
Healthy / Unhealthy
```

Health checks become especially important in automated deployments.

---

# 67. Container Networking Lab

Create two containers:

```
Container A → web
Container B → test client
```

Place them on the same container network.

Verify:

```
IP addresses
Routing
DNS/service discovery
Connectivity
```

For Docker, a conceptual workflow is:

```
docker network create labnet
docker run -d --name web --network labnet nginx
docker run --rm -it --network labnet alpine sh
```

From the second container, investigate whether the first container can be reached.

Your objective is not simply to make the command work. Explain:

```
What network namespace does each container use?
What virtual interface does it have?
What bridge or network connects them?
How is name resolution performed?
```

---

# 68. Container Storage Lab

Create a container that writes data to a mounted volume.

For example, create a volume:

```
docker volume create labdata
```

Run a container using it:

```
docker run --rm -v labdata:/data alpine sh -c 'echo "persistent data" > /data/test.txt'
```

Then create another container using the same volume:

```
docker run --rm -v labdata:/data alpine cat /data/test.txt
```

The second container should be able to access the data created by the first.

The important lesson is:

```
Container lifecycle
      ≠
Data lifecycle
```

Persistent data should have an independent lifecycle.

---

# 69. Container Resource Lab

Run a container and inspect its resource consumption.

With Docker:

```
docker stats
```

Observe:

```
CPU
Memory
Network
Block I/O
```

Then impose a memory limit on a test container.

For example, conceptually:

```
docker run --memory=256m ...
```

Observe the difference.

Your objective is to understand why resource limits are necessary in multi-tenant environments.

---

# 70. Dockerfile Lab

Create a simple application.

For example:

```
app/
├── Dockerfile
└── app.py
```

The application can print a message or run a minimal HTTP service.

Build it:

```
docker build -t lab-app .
```

Run it:

```
docker run --rm lab-app
```

Then modify the application and rebuild.

Observe how image layers are created and reused.

The purpose of this exercise is to understand the relationship:

```
Source Code
     ↓
Dockerfile
     ↓
Image
     ↓
Container
```

---

# 71. Podman Lab

Repeat a basic container workflow with Podman.

For example:

```
podman pull nginx
podman run -d --name web -p 8080:80 nginx
podman ps
podman logs web
podman stop web
podman rm web
```

Compare the workflow with Docker.

Ask yourself:

```
Where is the daemon?
Who owns the container process?
Can an ordinary user run it?
Where is container storage located?
How does networking differ?
```

The goal is to understand architecture rather than merely memorize equivalent commands.

---

# 72. LXC Lab

In a suitable Linux laboratory environment, install LXC and create a test system container.

After starting it, inspect:

```
Hostname
Processes
Network interfaces
Filesystem
Users
Resource limits
```

Compare the environment to the host.

From inside the container, ask:

```
What processes can I see?
What disks can I see?
What hostname do I see?
What network interfaces do I see?
Who appears to be root?
```

Then inspect the same container from the host.

This demonstrates namespace-based isolation directly.

---

# 73. LXD Lab

Create a small LXD test environment.

Explore the concepts of:

```
Images
Instances
Profiles
Networks
Storage pools
```

A useful exercise is to create a container, modify its configuration, stop it, and recreate another container from the same image.

Then examine how LXD separates:

```
Image
Instance
Storage
Network
Configuration
```

This teaches infrastructure-style container management.

---

# 74. Comparing the Four Technologies in Practice

A useful exercise is to create the same conceptual workload using:

```
LXC
LXD
Docker
Podman
```

For each technology, document:

```
How is the image/root filesystem created?
How is the container started?
How is networking configured?
How is storage persisted?
How are CPU/RAM limits applied?
How are logs inspected?
How is the container stopped?
How is the container deleted?
```

You will quickly discover that the underlying concepts are similar while the management models differ.

---

# 75. Containers and Virtual Machines: Performance

Containers generally have less overhead because they do not require a separate guest kernel.

A VM has:

```
Guest OS
Guest Kernel
Virtual Hardware
Virtual Drivers
```

A container generally has:

```
User-space filesystem
Application
Host kernel
```

However, it is incorrect to conclude that containers are always faster.

Performance depends on:

```
Workload
Storage
Networking
CPU scheduling
Memory
Runtime
Kernel
Application architecture
```

Container overhead is usually lower, but the application still competes for physical resources.

---

# 76. Containers and Virtual Machines: Security

VMs typically provide a stronger isolation boundary because each guest has a separate kernel.

Containers share the host kernel.

This does not mean containers are insecure. Properly configured containers can provide strong isolation.

But the security model differs:

```
VM:
Application
   ↓
Guest Kernel
   ↓
Virtual Hardware
   ↓
Hypervisor
   ↓
Host

Container:
Application
   ↓
Container Isolation
   ↓
Shared Host Kernel
   ↓
Host
```

A kernel compromise is therefore particularly significant in container environments.

---

# 77. Containers and Virtual Machines: Portability

A VM image includes a complete operating-system environment.

A container image usually includes application user space but depends on the host kernel.

Therefore:

```
VM portability
→ More independent of host OS kernel

Container portability
→ More dependent on compatible host kernel/runtime
```

Container images can nevertheless be extremely portable across Linux environments that provide compatible runtimes and kernel features.

---

# 78. Containers and Virtual Machines: Density

Because containers generally require fewer resources, a host can often run many more containers than full VMs.

For example:

```
Physical Host
├── VM 1
├── VM 2
└── VM 3
```

might become:

```
Physical Host
├── Container 1
├── Container 2
├── Container 3
├── Container 4
├── Container 5
├── ...
└── Container 100
```

The actual density depends entirely on workload resource requirements.

High density is valuable, but it also increases the importance of:

```
Resource limits
Monitoring
Scheduling
Security
Failure management
```

---

# 79. Containers and Microservices

Containers are closely associated with microservices.

A monolithic application might look like:

```
One Server
└── Large Application
```

A containerized architecture might look like:

```
Container A → Authentication
Container B → API
Container C → Database
Container D → Cache
Container E → Worker
```

Each service can potentially be:

```
Built independently
Deployed independently
Scaled independently
Updated independently
```

However, microservices introduce operational complexity.

More containers mean more:

```
Networks
Logs
Configurations
Monitoring targets
Failure modes
Deployments
```

Containers make microservices possible at scale, but they do not automatically make the architecture better.

---

# 80. Containers and CI/CD

Containers are highly useful in continuous integration and continuous delivery.

A typical pipeline is:

```
Developer
   ↓
Git Repository
   ↓
Build
   ↓
Container Image
   ↓
Tests
   ↓
Security Scan
   ↓
Registry
   ↓
Deployment
```

The same image can then move through environments:

```
Development
     ↓
Testing
     ↓
Staging
     ↓
Production
```

This reduces differences between environments.

The artifact being promoted is the image itself.

---

# 81. Infrastructure as Code and Containers

Containerized environments are often managed declaratively.

Instead of manually configuring a server, you describe:

```
Services
Networks
Volumes
Environment
Resources
```

and let tooling create the desired state.

This fits well with Infrastructure as Code and configuration management.

The broader principle is:

> Define infrastructure as reproducible configuration rather than undocumented manual steps.

Containers strongly reinforce this operational model.

---

# 82. Common Beginner Mistakes

One common mistake is thinking that a container is a small VM.

It is not. A container normally shares the host kernel.

Another mistake is assuming that deleting a container deletes all application data. This depends on where the data is stored.

Another mistake is putting databases and other persistent data only in the container writable layer.

Another mistake is running everything as root inside containers when the application does not need that privilege.

Another mistake is assuming that an image is the same as a running container.

Another mistake is assuming that containers remove the need for system administration. Containerized environments still require careful management of:

```
Linux
Networking
Storage
Security
CPU
Memory
Backups
Monitoring
Updates
```

---

# 83. Common Security Mistakes

Avoid using privileged containers unless the workload genuinely requires it.

Avoid mounting sensitive host directories unnecessarily.

Avoid giving containers unnecessary Linux capabilities.

Avoid embedding passwords and API keys into images.

Avoid using untrusted images without verifying their provenance.

Avoid assuming that network isolation exists automatically.

Avoid ignoring kernel updates because "the applications are inside containers."

The container runtime is part of the host security boundary.

---

# 84. Troubleshooting: Container Cannot Start

Suppose:

```
docker run myapp
```

fails.

Do not immediately recreate the container repeatedly.

Investigate:

```
Image availability
Container logs
Configuration
Environment variables
Port conflicts
Volume mounts
Permissions
Resource limits
Runtime errors
```

For example:

```
docker ps -a
docker logs myapp
docker inspect myapp
```

A container that exits immediately is not necessarily broken. It may simply be running an application that terminates immediately.

---

# 85. Troubleshooting: Port Already in Use

Suppose you receive an error indicating that a host port is already allocated.

The problem might be:

```
Container A → Host port 8080
Container B → Host port 8080
```

Two different processes cannot normally bind the same host IP/port combination simultaneously.

You can either:

```
Stop the conflicting service
```

or:

```
Publish another host port
```

For example:

```
Host 8080 → Container A:80
Host 8081 → Container B:80
```

The containers can still use port 80 internally.

---

# 86. Troubleshooting: Container Cannot Reach Database

Suppose:

```
Application container
       ↓
Database container
```

but the application cannot connect.

Investigate systematically:

```
Are both containers running?
Are they on the same network?
Is the database listening?
Is the correct port used?
Is DNS resolving?
Are credentials correct?
Is the database ready?
Is a firewall blocking traffic?
```

Do not begin by changing random IP addresses.

Container networking should be understood as a sequence of namespaces, interfaces, bridges, routes, and service endpoints.

---

# 87. Troubleshooting: Container Has No Data

A database container is recreated and its data disappears.

The likely architectural problem is that the data was stored inside the disposable container layer.

The correct architecture is:

```
Database Container
       │
       ▼
Persistent Volume
       │
       ▼
Persistent Storage
```

The container can then be recreated:

```
Delete old container
       ↓
Create new container
       ↓
Attach same volume
       ↓
Data remains
```

This is one of the most important practical container concepts.

---

# 88. Troubleshooting: Container Consumes Too Much Memory

Suppose a host has:

```
64 GB RAM
```

and one container begins consuming almost all available memory.

Investigate:

```
Container memory usage
Application behavior
Cgroup limits
Memory leaks
Swap configuration
Host memory pressure
```

A resource limit can protect the host from one workload consuming all memory.

However, simply setting an arbitrary low memory limit can cause the application to fail.

Resource limits should be based on observed workload requirements.

---

# 89. Container Storage and the Underlying Host

A container's storage is still ultimately physical storage.

The hierarchy may be:

```
Container
   ↓
Container writable layer / volume
   ↓
Container storage driver
   ↓
Host filesystem or block storage
   ↓
Physical disk / datastore
```

This connects container administration to the previous chapter.

Container storage performance can therefore depend on:

```
Filesystem
Storage driver
Host disk
RAID
SSD/NVMe
Network storage
Datastore
```

Containers do not eliminate storage architecture.

---

# 90. Containers on Virtualized Infrastructure

A common production design is:

```
Physical Server
       ↓
Hypervisor
       ↓
Linux VM
       ↓
Container Runtime
       ↓
Containers
```

For example:

```
Physical Server
       ↓
KVM / Proxmox
       ↓
Ubuntu VM
       ↓
Podman
       ↓
Application Containers
```

This architecture combines two layers:

```
VM → infrastructure isolation
Container → application isolation
```

The VM can provide a strong boundary around the operating system while containers provide efficient application packaging.

---

# 91. Container Orchestration

As the number of containers grows, manually managing them becomes difficult.

Imagine:

```
5 containers
```

Manual administration may be manageable.

But with:

```
500 containers
```

you need automation for:

```
Scheduling
Deployment
Networking
Service discovery
Scaling
Health checks
Rolling updates
Failure recovery
Secrets
Configuration
```

This leads to orchestration platforms.

Kubernetes is a major example, but orchestration is beyond the scope of this introductory chapter.

The important point is that Docker, Podman, LXC, and LXD are container-management technologies, while large-scale orchestration introduces another layer of infrastructure.

---

# 92. A Complete Mental Model

At this point, you should visualize a container environment like this:

```
Physical Hardware
       │
       ▼
Linux Kernel
       │
       ├─────────────────────────────┐
       │                             │
       ▼                             ▼
Namespaces                       Cgroups
       │                             │
       └──────────────┬──────────────┘
                      ▼
                Container Runtime
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
      Container A Container B Container C
          │           │           │
       App A        App B       App C
          │           │           │
          └──────┬────┴────┬──────┘
                 ▼         ▼
              Network    Storage
```

This is the core architecture you should remember.

---

# 93. Practical Lab — VM Versus Container

Create:

```
One Linux VM
One Linux container
```

Inside both environments, inspect:

```
uname -a
hostname
ps aux
ip addr
df -h
```

Compare the results.

Ask:

```
Does the VM have its own kernel?
Does the container have its own kernel?
What processes can each environment see?
What filesystem does each environment see?
How are network interfaces represented?
```

This exercise makes the theoretical difference between virtualization and containerization concrete.

---

# 94. Practical Lab — PID Isolation

Start a container with a shell.

Inside the container:

```
ps aux
```

On the host:

```
ps aux
```

Compare the process lists.

The host should be able to see the container process, while the ordinary container process view is isolated.

This demonstrates the hierarchical nature of PID namespaces.

---

# 95. Practical Lab — Resource Isolation

Run a container with a memory limit.

Monitor:

```
docker stats
```

or:

```
podman stats
```

Then create controlled memory pressure in the container.

Observe how the runtime and kernel enforce the configured resource boundary.

Your objective is to understand:

```
Application
   ↓
Cgroup
   ↓
Kernel resource control
```

rather than treating the runtime command as a magic feature.

---

# 96. Practical Lab — Persistent Database

Deploy a PostgreSQL container with persistent storage.

The architecture should be:

```
PostgreSQL Container
       │
       ▼
Persistent Volume
       │
       ▼
Host Storage
```

Create test data.

Then:

```
Stop container
Remove container
Create replacement container
Attach same volume
```

Verify that the database data remains.

This lab teaches the difference between:

```
Application lifecycle
```

and:

```
Data lifecycle
```

---

# 97. Practical Lab — Image Reproducibility

Build a small image from a Dockerfile.

Then delete the resulting container.

Recreate it from the same image.

Verify that the application behaves consistently.

Modify the Dockerfile, rebuild the image, and compare the result.

This demonstrates the principle:

```
Declarative Build
      ↓
Immutable Artifact
      ↓
Reproducible Container
```

This principle is central to modern CI/CD.

---

# 98. Practical Lab — Security Inspection

For a test container, investigate:

```
User identity
Capabilities
Mounted directories
Network configuration
Resource limits
Filesystem permissions
```

Ask:

```
Does the container run as root?
Does it really need root?
What host directories are mounted?
What capabilities are granted?
Can the filesystem be read-only?
Are resource limits configured?
```

The goal is to develop the habit of treating every container as a security boundary that must be explicitly designed.

---

# 99. Practical Lab — Docker and Podman Comparison

Run the same application using Docker and Podman.

Compare:

```
Image management
Container creation
Networking
Volumes
Logs
Process ownership
Rootless execution
Lifecycle management
```

Create a table documenting your observations.

The objective is not to decide that one tool is "better."

The objective is to understand why different container engines exist and which operational model each one emphasizes.

---

# 100. Practical Lab — LXC/LXD and Docker Comparison

Create:

```
One LXC/LXD system container
One Docker/Podman application container
```

Inside the system container, explore:

```
Users
Init system
SSH
Services
Filesystem
Networking
```

Inside the application container, explore:

```
Main process
Filesystem
Logs
Networking
Volumes
```

Then explain in your own words:

> Why does the LXC/LXD container feel more like a small server while the Docker/Podman container feels more like an application process?

If you can answer this clearly, you understand one of the most important conceptual distinctions in this chapter.

---

# 101. Design Exercise — Web Application

Design a small containerized Web application.

Requirements:

```
Web frontend
Application backend
Database
Persistent database storage
Private internal network
Public HTTP access
Resource limits
```

Draw the architecture:

```
                    Internet
                       │
                       ▼
                 Web Container
                       │
                       ▼
              Application Container
                       │
                       ▼
                Database Container
                       │
                       ▼
                Persistent Volume
```

Then answer:

```
Which components should be publicly accessible?
Which components should communicate only internally?
Which data must survive container replacement?
What resources should be limited?
How should logs be collected?
What should happen if the database container stops?
```

This exercise connects container technology to real system design.

---

# 102. Design Exercise — VM or Container?

For each workload, decide whether a VM, container, or combination is appropriate.

### Workload A

A Windows Server application must run on a Linux physical server.

The appropriate solution is generally a VM because the workload requires a different operating-system kernel.

### Workload B

A Python Web application needs to be deployed consistently across development and production.

A container is an excellent candidate because the application and dependencies can be packaged as a reproducible image.

### Workload C

A laboratory requires testing Linux kernel modifications.

A VM is generally more appropriate because kernel experimentation should be isolated from the production host kernel.

### Workload D

A Linux server needs several lightweight isolated environments, each behaving like a small Linux system.

LXC/LXD may be appropriate because system containers are designed for this style of workload.

The goal is to select technology based on requirements rather than following trends.

---

# 103. Key Concepts to Memorize

You should remember these relationships:

```
VM
→ Virtualizes hardware
→ Usually has its own kernel

Container
→ Isolates processes
→ Usually shares the host kernel
```

And:

```
Namespaces
→ Isolation / visibility

Cgroups
→ Resource control / accounting

Image
→ Reusable application filesystem/template

Container
→ Running instance

Volume
→ Persistent data

Registry
→ Image distribution

Runtime
→ Creates and manages containers
```

For the technologies:

```
LXC
→ Linux system containers

LXD
→ Higher-level management of system containers and VMs

Docker
→ Application-focused container workflow

Podman
→ Daemonless/rootless-oriented container engine
```

---

# 104. Final Synthesis

Containers are not simply small virtual machines.

A virtual machine creates a virtual hardware environment and normally runs an independent guest kernel:

```
Hardware
   ↓
Hypervisor
   ↓
Virtual Hardware
   ↓
Guest Kernel
   ↓
Application
```

A container creates an isolated process environment using the host operating system's kernel:

```
Hardware
   ↓
Host Kernel
   ↓
Namespaces + Cgroups + Security Controls
   ↓
Container Runtime
   ↓
Application
```

This difference produces the main advantages of containers:

```
Low overhead
Fast startup
High density
Reproducible application environments
Efficient deployment
Easy replacement
```

But it also creates important limitations:

```
Shared kernel
Kernel compatibility requirements
Different security boundary
Need for careful persistent storage
Need for resource control
Need for image security
```

LXC provides Linux container technology oriented toward system containers.

LXD provides a higher-level management experience for system containers and virtual machines.

Docker popularized the application-container workflow based around images, containers, registries, volumes, and networks.

Podman provides a modern daemonless container engine with strong support for rootless operation and OCI-compatible workflows.

The most important conceptual chain is:

```
Container Image
      ↓
Container Runtime
      ↓
Namespaces
      +
Cgroups
      +
Capabilities / Security Controls
      ↓
Container
      ↓
Application
```

And the most important comparison is:

```
Virtual Machine
────────────────────────────
Virtual hardware
Independent guest kernel
Full operating system
Higher overhead
Strong isolation boundary


Container
────────────────────────────
Process isolation
Shared host kernel
User-space environment
Lower overhead
Different isolation boundary
```

---

# 105. Knowledge Check

Before moving to the next chapter, make sure you can answer these questions without looking at the lesson.

1. What is a container?
    
2. Why does a container normally not require its own kernel?
    
3. What is the difference between a container and a virtual machine?
    
4. What are Linux namespaces?
    
5. What is a PID namespace?
    
6. What is a network namespace?
    
7. What is a mount namespace?
    
8. What are cgroups?
    
9. What is the difference between namespace isolation and cgroup resource control?
    
10. What is a container image?
    
11. What is the difference between an image and a container?
    
12. Why should persistent application data normally be separated from the container lifecycle?
    
13. What is a container volume?
    
14. What is a bind mount?
    
15. Why is container networking based on network namespaces?
    
16. What is port publishing?
    
17. Why is a container not simply a lightweight VM?
    
18. When would you choose a VM instead of a container?
    
19. What is LXC?
    
20. What is LXD?
    
21. How does LXD relate conceptually to LXC?
    
22. What is Docker primarily designed to facilitate?
    
23. What is a Dockerfile?
    
24. What is a container registry?
    
25. What is Podman?
    
26. What does daemonless mean in the context of Podman?
    
27. What are rootless containers?
    
28. Why are Linux capabilities relevant to container security?
    
29. What does seccomp provide?
    
30. Why are image vulnerabilities still important even when containers are isolated?
    
31. Why should secrets not normally be embedded in container images?
    
32. Why are resource limits important?
    
33. Why might a container be unable to access another container?
    
34. Why can a containerized database lose data when its container is deleted?
    
35. Why can containers and VMs be used together?
    

---

# 106. Final Mental Model

If you remember only one architecture from this chapter, remember this:

```
                         PHYSICAL HARDWARE
                                │
                                ▼
                          LINUX KERNEL
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
              ▼                                   ▼
         NAMESPACES                            CGROUPS
       "What can I see?"                 "What can I use?"
              │                                   │
              └─────────────────┬─────────────────┘
                                ▼
                       CONTAINER RUNTIME
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
         Container A       Container B       Container C
              │                 │                 │
              ▼                 ▼                 ▼
           App A             App B             App C
              │                 │                 │
              └────────────┬────┴────┬────────────┘
                           │         │
                           ▼         ▼
                        NETWORK   STORAGE
```

And compare it with:

```
                         PHYSICAL HARDWARE
                                │
                                ▼
                           HYPERVISOR
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
             VM A              VM B              VM C
              │                 │                 │
         Guest Kernel       Guest Kernel       Guest Kernel
              │                 │                 │
          User Space         User Space         User Space
              │                 │                 │
             App               App               App
```

The fundamental distinction is therefore:

> **A virtual machine virtualizes a computer. A container isolates processes inside an operating-system kernel.**

Once this distinction is clear, LXC, LXD, Docker, and Podman become much easier to understand because you can evaluate them according to the problem they solve rather than treating them as four unrelated technologies.