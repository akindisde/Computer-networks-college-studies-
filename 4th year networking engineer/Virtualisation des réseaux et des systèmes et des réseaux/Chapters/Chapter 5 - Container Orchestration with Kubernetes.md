# Chapter 5 — Container Orchestration with Kubernetes

## Introduction

Running one or two containers manually is relatively simple. You can start a container, inspect its logs, stop it, restart it, and remove it when you no longer need it. The situation changes dramatically when an application consists of dozens or hundreds of containers distributed across multiple machines.

Imagine an application composed of a frontend, several backend services, a database, background workers, a cache, and monitoring components. If containers are distributed across several servers, someone or something must decide where each container runs, restart failed containers, connect services together, distribute traffic, maintain the desired number of replicas, and perform application updates without unnecessary downtime.

This is the problem that **container orchestration** addresses.

Kubernetes is one of the most important container orchestration platforms. It provides a declarative control plane through which an administrator or application team describes the desired state of an application. Kubernetes then continuously works to make the actual cluster state converge toward that desired state.

The central idea is therefore not simply:

> "Start this container."

It is:

> "I want this application to be running in this state, with this number of instances, this network exposure, these resources, and this update strategy."

Kubernetes continuously reconciles the system toward that desired state.

This chapter introduces orchestration, explains the architecture of Kubernetes, teaches the core objects required to manage containers, and then focuses on services, replicas, rolling updates, and rollbacks.

---

# 1. Learning Objectives

By the end of this chapter, you should be able to explain why container orchestration becomes necessary as applications grow.

You should understand the architecture of a Kubernetes cluster, including the control plane, worker nodes, API server, scheduler, controllers, and data store.

You should understand the difference between a container, a Pod, a Deployment, and a Service.

You should be able to create and inspect a basic Kubernetes Deployment and explain how Kubernetes maintains the requested number of replicas.

You should understand how Kubernetes Services provide stable network access to dynamically created Pods.

You should be able to perform a rolling update and explain what Kubernetes is doing during the update.

You should understand how to detect a failed deployment and perform a rollback to a previous revision.

Finally, you should be able to reason about Kubernetes declaratively rather than treating it as simply a collection of container commands.

---

# 2. Why Container Orchestration Is Necessary

Consider a simple application:

```
One Server
   │
   └── One Container
```

A container runtime is enough.

Now consider:

```
3 Servers
   │
   ├── 10 Web Containers
   ├── 8 API Containers
   ├── 3 Worker Containers
   ├── 2 Cache Containers
   └── 2 Monitoring Containers
```

Several new questions appear immediately.

Where should each container run?

What happens if a server fails?

What happens if a container crashes?

How do users reach the correct application instances?

How do we distribute traffic?

How do we maintain three copies of an application?

How do we update the application without taking the entire service offline?

How do we undo an update that introduces a bug?

Manually solving these problems is possible at small scale, but the operational complexity grows quickly.

Orchestration automates these responsibilities.

---

# 3. What Is Container Orchestration?

Container orchestration is the automated management of containerized workloads across infrastructure.

An orchestration platform can provide capabilities such as:

```
Scheduling
Container lifecycle management
Desired-state reconciliation
Scaling
Service discovery
Load balancing
Health monitoring
Failure recovery
Rolling updates
Rollbacks
Resource management
Configuration management
Secret management
```

The important word is **automation**.

Instead of an administrator manually monitoring every container, the orchestration platform continuously evaluates the current state of the system.

---

# 4. The Desired-State Model

Kubernetes is fundamentally based on desired state.

Suppose you tell Kubernetes:

```
Application:
    web

Desired replicas:
    3
```

Kubernetes interprets this as:

```
I want 3 healthy instances of this workload.
```

If only two are running:

```
Desired: 3
Actual: 2
```

Kubernetes attempts to create another one.

If four are running:

```
Desired: 3
Actual: 4
```

Kubernetes can reduce the number to three.

The platform continuously tries to reconcile:

```
Desired State
      ↓
Controller
      ↓
Actual State
```

This is one of the most important concepts in Kubernetes.

---

# 5. Imperative Versus Declarative Management

There are two useful ways to think about infrastructure management.

An imperative approach tells the system what action to perform:

```
Create a container.
Start it.
Create another container.
Connect it to the network.
```

A declarative approach describes what should exist:

```
There should be 3 replicas
of version 2.0
of this application.
```

Kubernetes strongly emphasizes declarative configuration.

For example:

```
replicas: 3
```

does not mean:

> "Create exactly three containers once."

It means:

> "The desired state contains three replicas."

Kubernetes then continuously works to maintain that state.

---

# 6. Kubernetes in One Architecture Diagram

A simplified Kubernetes architecture looks like this:

```
                         Kubernetes Cluster
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
              ▼                                   ▼
        Control Plane                         Worker Nodes
              │                                   │
     ┌────────┼────────┐              ┌───────────┼───────────┐
     │        │        │              │           │           │
     ▼        ▼        ▼              ▼           ▼           ▼
   API     Scheduler Controllers    kubelet    kubelet      kubelet
  Server
     │
     ▼
   etcd
```

The control plane makes cluster-wide decisions and maintains the desired state.

Worker nodes execute application workloads.

---

# 7. The Kubernetes Control Plane

The control plane is responsible for managing the cluster.

Important components include:

```
kube-apiserver
etcd
kube-scheduler
kube-controller-manager
```

Modern Kubernetes installations can also contain additional controllers and cloud-provider components.

The control plane does not normally run application containers itself in the same way that worker nodes do. Its primary purpose is to coordinate the cluster.

---

# 8. The Kubernetes API Server

The Kubernetes API server is the central interface to the cluster.

When you execute:

```
kubectl apply -f deployment.yaml
```

the request is sent to the Kubernetes API.

Conceptually:

```
kubectl
   ↓
API Server
   ↓
Cluster State
```

The API server validates requests, authenticates users, authorizes operations, and exposes Kubernetes resources through the API.

This is why Kubernetes is often described as an API-driven platform.

---

# 9. etcd

Kubernetes needs persistent storage for cluster state.

This role is provided by **etcd**, a distributed key-value store.

Conceptually:

```
Kubernetes API
      ↓
    etcd
      ↓
Cluster State
```

The cluster's desired and important control-plane state is stored through the Kubernetes API in etcd.

Examples of information represented in cluster state include:

```
Deployments
Pods
Services
ConfigMaps
Secrets
Node information
Configuration
```

Because etcd is critical to the control plane, protecting and backing it up is an important administrative responsibility.

---

# 10. The Scheduler

The Kubernetes scheduler decides which worker node should run a newly created Pod when no node has yet been assigned.

Suppose:

```
Worker A → 80% CPU
Worker B → 20% CPU
Worker C → 40% CPU
```

A new Pod arrives.

The scheduler evaluates available nodes according to resource requests, constraints, affinity rules, taints and tolerations, topology considerations, and other scheduling information.

A simplified decision is:

```
New Pod
   ↓
Scheduler
   ↓
Suitable Node
```

The scheduler does not simply choose the node with the smallest CPU percentage. Real scheduling decisions are based on multiple constraints.

---

# 11. Worker Nodes

Worker nodes are the machines that execute application workloads.

A simplified worker node contains:

```
Worker Node
├── kubelet
├── Container Runtime
├── Network Components
└── Pods
```

The worker node may be a physical server or a virtual machine.

For example:

```
Physical Server
      ↓
Virtual Machine
      ↓
Linux
      ↓
Kubernetes Worker Node
      ↓
Pods
```

This demonstrates how the virtualization and container technologies studied in previous chapters fit together.

---

# 12. kubelet

The kubelet is the main Kubernetes agent running on a worker node.

Its role is to ensure that the Pods assigned to the node are running according to their specifications.

Conceptually:

```
API Server
     ↓
Desired Pod assignment
     ↓
kubelet
     ↓
Container Runtime
     ↓
Containers
```

The kubelet monitors the local workloads and reports status back to the Kubernetes control plane.

---

# 13. Container Runtime

Kubernetes needs a container runtime to actually run containers.

The runtime is responsible for operations such as:

```
Pulling images
Creating containers
Starting containers
Stopping containers
Managing container execution
```

Kubernetes itself is not simply "Docker."

Modern Kubernetes commonly uses runtimes compatible with the Kubernetes Container Runtime Interface (CRI), such as containerd or CRI-O.

This distinction is important:

```
Kubernetes
→ Orchestration

Container Runtime
→ Container execution
```

Kubernetes coordinates workloads; the runtime executes containers.

---

# 14. Pods

The **Pod** is the smallest deployable unit in Kubernetes.

This is a critical concept.

Kubernetes does not primarily schedule individual containers. It schedules Pods.

A Pod normally contains one main application container, but it can contain multiple tightly coupled containers that need to share the same network namespace and storage context.

For example:

```
Pod
├── Application Container
└── Sidecar Container
```

The containers inside a Pod share certain resources.

---

# 15. Why Kubernetes Uses Pods

Suppose an application requires:

```
Main Application
+
Logging Sidecar
```

These containers may need:

```
Shared network namespace
Shared volumes
Shared lifecycle
```

A Pod provides that grouping.

Conceptually:

```
              Pod
       ┌───────────────┐
       │               │
       │ App Container │
       │               │
       │ Sidecar       │
       │ Container     │
       │               │
       └───────────────┘
               │
          Shared context
```

However, putting multiple unrelated applications into one Pod is generally poor design.

---

# 16. Pod Networking

Each Pod normally receives an IP address in the Kubernetes cluster network.

Containers within the same Pod share the Pod's network namespace.

Therefore:

```
Pod
├── Container A
└── Container B
```

can communicate through:

```
localhost
```

because they share the same network namespace.

For example:

```
Container A → localhost:8080 → Container B
```

This is different from two separate Pods, which normally communicate through their Pod IPs or, more commonly, through a Kubernetes Service.

---

# 17. Pods Are Usually Ephemeral

A Pod should generally not be treated as a permanent server.

A Pod can be:

```
Created
Scheduled
Started
Stopped
Deleted
Recreated
```

If a Pod disappears, Kubernetes may create a replacement.

This is why Kubernetes applications should not depend on the permanent identity of an individual Pod.

For example:

```
web-pod-abc123
```

might disappear and be replaced with:

```
web-pod-def456
```

The application should continue working without requiring users to know the Pod's identity.

---

# 18. Deployments

A **Deployment** is one of the most important Kubernetes resources for stateless applications.

It manages a set of Pods and provides declarative control over:

```
Number of replicas
Pod template
Application image
Update strategy
Revision history
```

A simplified hierarchy is:

```
Deployment
    ↓
ReplicaSet
    ↓
Pods
    ↓
Containers
```

The Deployment usually does not create Pods directly. It manages ReplicaSets, which manage the Pods.

---

# 19. ReplicaSets

A ReplicaSet maintains a specified number of Pod replicas.

Suppose:

```
replicas: 3
```

The ReplicaSet's responsibility is essentially:

```
Desired → 3 Pods
Actual  → 3 Pods
```

If one Pod fails:

```
Desired → 3
Actual  → 2
```

the ReplicaSet attempts to create a replacement.

This is self-healing at the workload level.

---

# 20. Why Deployments Manage ReplicaSets

When an application is updated, Kubernetes needs to maintain revision history.

Suppose:

```
Revision 1 → nginx:1.25
Revision 2 → nginx:1.26
```

The Deployment can create a new ReplicaSet for the new version.

Conceptually:

```
Deployment
    │
    ├── ReplicaSet v1
    │      └── Old Pods
    │
    └── ReplicaSet v2
           └── New Pods
```

This architecture enables rolling updates and rollbacks.

---

# 21. A Basic Deployment

A basic Deployment might look like:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:1.27
          ports:
            - containerPort: 80
```

The YAML expresses desired state.

It says approximately:

```
Create a Deployment named web.

Maintain 3 replicas.

Use Pods labeled app=web.

Run nginx:1.27 in each Pod.
```

It does not tell Kubernetes exactly which node to use or exactly how to create each Pod. Kubernetes determines those implementation details.

---

# 22. Applying a Manifest

Save the Deployment as:

```
web-deployment.yaml
```

Then apply it:

```
kubectl apply -f web-deployment.yaml
```

Inspect it:

```
kubectl get deployment
kubectl get replicasets
kubectl get pods
```

You should see the relationship:

```
Deployment
    ↓
ReplicaSet
    ↓
3 Pods
```

This is one of the first workflows you should practice in Kubernetes.

---

# 23. Inspecting a Pod

Use:

```
kubectl get pods
```

for a concise overview.

For more information:

```
kubectl describe pod <pod-name>
```

This can reveal:

```
Node assignment
Container image
Events
Conditions
Volumes
Probes
Resource information
```

When troubleshooting Kubernetes, the `describe` output and events are often extremely useful.

---

# 24. Kubernetes Services

Pods are ephemeral.

Their IP addresses can change.

Therefore, applications should not normally connect directly to individual Pod IP addresses.

A **Service** provides a stable network endpoint for a group of Pods.

Conceptually:

```
                Service
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
        Pod A    Pod B    Pod C
```

The Service selects Pods using labels.

This creates a stable abstraction between clients and dynamic Pods.

---

# 25. Service Selectors

Suppose Pods have:

```
labels:
  app: web
```

A Service can use:

```
selector:
  app: web
```

The Service then targets Pods matching that label.

This is an important Kubernetes principle:

> Labels identify resources; selectors connect resources.

If a Pod does not match the Service selector, it will not normally receive traffic through that Service.

---

# 26. Basic ClusterIP Service

A typical internal Service might look like:

```
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

A `ClusterIP` Service provides an internal cluster endpoint.

The architecture becomes:

```
Application
    ↓
Service:80
    ↓
┌───┴────┬────────┐
▼        ▼        ▼
Pod A   Pod B    Pod C
```

The Service abstracts the individual Pod addresses.

---

# 27. Service Port Versus Target Port

These two concepts are easy to confuse.

Suppose:

```
ports:
  - port: 80
    targetPort: 8080
```

The meaning is:

```
Service port → 80
Pod target port → 8080
```

The traffic path is:

```
Client
   ↓
Service:80
   ↓
Pod:8080
```

The application inside the Pod must actually listen on the target port.

---

# 28. Service Types

Common Kubernetes Service types include:

```
ClusterIP
NodePort
LoadBalancer
ExternalName
```

`ClusterIP` is the standard internal service type.

`NodePort` exposes a service through a port on each node.

`LoadBalancer` is commonly used with cloud or external load-balancing integrations.

The exact behavior of `LoadBalancer` depends on the Kubernetes environment.

---

# 29. Service Discovery

Kubernetes commonly provides DNS-based service discovery.

Instead of using:

```
10.96.12.42
```

an application can often connect using a service name such as:

```
web
```

or a fully qualified Kubernetes service DNS name.

Conceptually:

```
Application
    ↓
DNS
    ↓
web.default.svc.cluster.local
    ↓
Service
    ↓
Pods
```

This means applications do not need to hard-code dynamically changing Pod addresses.

---

# 30. Replication

Replication means running multiple copies of a workload.

For example:

```
replicas: 3
```

creates three Pod instances.

The architecture is:

```
             Service
                │
        ┌───────┼───────┐
        ▼       ▼       ▼
      Pod 1   Pod 2   Pod 3
```

If the application is stateless, the three Pods can usually process requests independently.

This provides:

```
Higher availability
Load distribution
Capacity
Failure tolerance
```

---

# 31. Why Stateless Applications Scale Easily

Suppose each frontend Pod can process requests independently:

```
Pod A
Pod B
Pod C
```

A load-balancing Service can distribute requests between them.

If traffic increases:

```
3 replicas
   ↓
5 replicas
   ↓
10 replicas
```

The application can scale horizontally.

This is one of the main reasons containers and orchestration work well for Web applications.

---

# 32. Scaling a Deployment

You can scale a Deployment using:

```
kubectl scale deployment web --replicas=5
```

Then:

```
kubectl get pods
```

You should observe the number of Pods moving toward five.

This is an imperative command, but the resulting state is still represented in Kubernetes.

You can also declare the desired number in YAML:

```
spec:
  replicas: 5
```

The declarative file is often preferable for reproducible infrastructure.

---

# 33. Self-Healing

One of Kubernetes' major features is automatic reconciliation.

Suppose:

```
Desired replicas = 3
```

and one Pod crashes:

```
Pod A → Running
Pod B → Failed
Pod C → Running
```

The ReplicaSet sees:

```
Actual = 2
Desired = 3
```

and creates another Pod.

The resulting state returns toward:

```
Pod A
Pod C
Pod D
```

This does not mean Kubernetes prevents application failures. It means Kubernetes can automatically replace failed workload instances according to the configured desired state.

---

# 34. Node Failure

Now imagine a worker node fails.

Suppose:

```
Node A
├── Pod 1
└── Pod 2

Node B
├── Pod 3
└── Pod 4
```

If Node A becomes unavailable, Kubernetes can detect the node failure and, depending on the workload and cluster conditions, recreate affected Pods on available nodes.

This demonstrates why Kubernetes is more than a local container runtime.

It manages workloads across a cluster.

---

# 35. Readiness and Liveness

Kubernetes needs to know whether an application is usable.

Two important concepts are:

```
Readiness
Liveness
```

A **readiness probe** answers:

> Should this Pod currently receive traffic?

A **liveness probe** answers:

> Is this container still functioning sufficiently to remain alive?

These are not the same question.

---

# 36. Readiness Example

Suppose a Web application takes 20 seconds to initialize.

The container process may already be running, but the application may not yet be ready to serve requests.

A readiness probe can prevent traffic from reaching it until initialization is complete.

Conceptually:

```
Container starts
      ↓
Application initializes
      ↓
Readiness = false
      ↓
No Service traffic
      ↓
Application ready
      ↓
Readiness = true
      ↓
Traffic allowed
```

This is extremely important during rolling updates.

---

# 37. Liveness Example

Suppose an application becomes stuck while its process remains technically alive.

A liveness probe can detect that the application is no longer functioning correctly.

Kubernetes can then restart the container according to the configured policy.

Conceptually:

```
Application
     ↓
Liveness probe
     ↓
Failure
     ↓
Container restart
```

A poorly designed liveness probe can itself cause unnecessary restarts, so probes must be chosen carefully.

---

# 38. Rolling Updates

A rolling update changes an application version gradually instead of replacing all Pods at once.

Suppose the current version is:

```
web:v1
```

with three replicas:

```
Pod A → v1
Pod B → v1
Pod C → v1
```

We want:

```
web:v2
```

Kubernetes can gradually create new Pods:

```
v1 v1 v1
 ↓
v2 v1 v1
 ↓
v2 v2 v1
 ↓
v2 v2 v2
```

The exact sequence depends on update settings and readiness.

---

# 39. Why Rolling Updates Matter

Without a rolling update, a deployment might do:

```
Stop all old Pods
      ↓
Start all new Pods
```

This can cause downtime.

A rolling update instead attempts to maintain application availability while transitioning between versions.

This is particularly valuable for:

```
Web applications
APIs
Stateless services
Production systems
```

---

# 40. Deployment Update Strategy

A Deployment can use a rolling update strategy.

For example:

```
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1
```

These parameters influence how many Pods can be unavailable and how many additional Pods can exist during the update.

For three replicas:

```
replicas = 3
maxUnavailable = 1
maxSurge = 1
```

Kubernetes can temporarily create an additional Pod while replacing old ones.

---

# 41. Understanding maxUnavailable

`maxUnavailable` controls how many desired Pods may be unavailable during an update.

If:

```
replicas: 4
maxUnavailable: 1
```

Kubernetes should generally avoid having more than one desired replica unavailable at a time during the rolling update.

This helps balance:

```
Availability
```

against:

```
Update speed
```

A production application with strict availability requirements may choose a conservative update policy.

---

# 42. Understanding maxSurge

`maxSurge` controls how many additional Pods may temporarily exist above the desired replica count during a rolling update.

Suppose:

```
replicas = 3
maxSurge = 1
```

During the transition, Kubernetes may temporarily have:

```
4 Pods
```

while moving from the old ReplicaSet to the new ReplicaSet.

This allows Kubernetes to create new capacity before removing old capacity.

---

# 43. Rolling Update Lifecycle

Suppose:

```
Deployment
replicas = 3
version = v1
```

The process may look like:

```
Initial:
v1 v1 v1

Create new ReplicaSet:
v1 v1 v1
v2

Wait for v2 readiness:
v1 v1 v1
v2 READY

Remove one old Pod:
v1 v1
v2

Create another v2:
v1 v1
v2 v2

Continue:
v1
v2 v2 v2

Final:
v2 v2 v2
```

The exact sequence depends on readiness, update parameters, scheduling, and cluster conditions.

---

# 44. Deployment Revision History

A Deployment maintains revision information.

You can inspect revisions with:

```
kubectl rollout history deployment/web
```

This allows administrators to understand previous versions of the Deployment.

A useful operational model is:

```
Revision 1 → v1
Revision 2 → v2
Revision 3 → v3
```

If v3 is defective, the Deployment can potentially be rolled back.

---

# 45. Performing a Rollout

Suppose the Deployment currently uses:

```
image: nginx:1.27
```

You can update it declaratively by changing the manifest:

```
image: nginx:1.28
```

Then:

```
kubectl apply -f web-deployment.yaml
```

Alternatively, you can update the image imperatively:

```
kubectl set image deployment/web web=nginx:1.28
```

The Deployment controller detects the change and creates a new ReplicaSet.

---

# 46. Monitoring a Rollout

After starting an update, inspect its progress:

```
kubectl rollout status deployment/web
```

You can also inspect Pods:

```
kubectl get pods
```

and ReplicaSets:

```
kubectl get replicasets
```

This lets you observe the transition:

```
Old ReplicaSet
      ↓
New ReplicaSet
      ↓
Gradual replacement
```

Do not consider an update complete merely because the new Pods have been created. Readiness and application behavior matter.

---

# 47. Failed Rollouts

Suppose you update:

```
web:v1
```

to:

```
web:v2
```

but v2 contains an invalid configuration.

Possible symptoms include:

```
CrashLoopBackOff
Readiness failures
ImagePullBackOff
Application errors
Pods stuck in Pending
Service has insufficient ready endpoints
```

The Deployment may stop progressing depending on the failure.

This is why rollout monitoring is essential.

---

# 48. ImagePullBackOff

A common Kubernetes problem occurs when a node cannot obtain the required image.

For example:

```
image: private.registry.example/app:v4
```

If the image is unavailable or authentication is missing, the Pod may fail to start.

Inspect:

```
kubectl describe pod <pod-name>
```

Look at the Events section.

Possible causes include:

```
Wrong image name
Wrong tag
Registry unavailable
Authentication failure
Network failure
Image does not exist
```

This is a common troubleshooting scenario.

---

# 49. CrashLoopBackOff

`CrashLoopBackOff` generally means that a container has repeatedly started and then exited, and Kubernetes is backing off before restarting it.

Possible causes include:

```
Application configuration error
Missing environment variable
Bad command
Dependency unavailable
Incorrect permissions
Application bug
```

Inspect:

```
kubectl logs <pod-name>
```

and:

```
kubectl describe pod <pod-name>
```

The name itself is not the root cause. It describes the restart/backoff behavior.

---

# 50. Pending Pods

A Pod in `Pending` state has not successfully reached running execution.

Possible causes include:

```
Insufficient CPU
Insufficient memory
No suitable node
Taints
Affinity constraints
Unbound persistent volume
Scheduling constraints
```

Use:

```
kubectl describe pod <pod-name>
```

and inspect scheduling events.

The scheduler is often the key component involved in this problem.

---

# 51. Rollback

Suppose:

```
v1 → stable
v2 → deployed
v2 → defective
```

A rollback returns the Deployment to a previous revision.

For example:

```
kubectl rollout undo deployment/web
```

Kubernetes can restore the previous Deployment revision.

Conceptually:

```
v1
 ↓
v2
 ↓
Problem
 ↓
Rollback
 ↓
v1
```

The exact behavior depends on the Deployment revision history and configuration.

---

# 52. Rolling Back to a Specific Revision

First inspect history:

```
kubectl rollout history deployment/web
```

Then choose the appropriate revision:

```
kubectl rollout undo deployment/web --to-revision=2
```

The important operational principle is:

> Never roll back blindly.

First determine which revision was known to be stable.

---

# 53. Rollback Is Not the Same as Backup

This distinction is essential.

A Kubernetes rollback changes the application Deployment configuration back to a previous revision.

It does not necessarily restore:

```
Database contents
Persistent files
External systems
User-generated data
```

For example:

```
Deployment rollback
→ Restore application version

Database backup
→ Restore persistent data
```

They solve different problems.

---

# 54. Application Rollback and Database Compatibility

Suppose version 2 changes the database schema:

```
v1 → Database schema A
v2 → Database schema B
```

If v2 is deployed and the database is migrated to schema B, simply rolling the application back to v1 may not be safe.

You may have:

```
Application v1
      ↓
Expects schema A

Database
      ↓
Schema B
```

This can cause application failure.

Therefore, production deployment strategies must consider application and database compatibility.

This is one reason database migrations should be designed carefully for backward compatibility where possible.

---

# 55. Zero-Downtime Deployment Is Not Automatic

Kubernetes rolling updates help reduce downtime, but zero downtime is not guaranteed.

You also need:

```
Healthy application
Correct readiness probes
Enough replicas
Correct update strategy
Sufficient cluster capacity
Working networking
Correct application shutdown behavior
Compatible database changes
```

A rolling update cannot compensate for an application that fails immediately after startup.

---

# 56. Graceful Shutdown

When Kubernetes removes a Pod, the application should ideally shut down gracefully.

A typical process is:

```
Pod termination requested
        ↓
Application receives termination signal
        ↓
Application stops accepting new work
        ↓
Existing requests finish
        ↓
Process exits
```

Applications that ignore graceful termination may cause:

```
Dropped connections
Failed requests
Incomplete jobs
Corrupted work
```

This becomes especially important during rolling updates.

---

# 57. Labels

Labels are key-value metadata attached to Kubernetes resources.

Example:

```
labels:
  app: web
  environment: production
  version: v2
```

Labels can be used for:

```
Service selection
Organization
Filtering
Deployment selection
Operational queries
```

For example:

```
kubectl get pods -l app=web
```

This returns Pods matching the label.

---

# 58. Selectors

Selectors are used to identify resources based on labels.

For example:

```
selector:
  matchLabels:
    app: web
```

means:

```
Find resources where:
app = web
```

A Deployment uses selectors to associate Pods with its ReplicaSet management.

A Service uses selectors to determine which Pods should receive traffic.

Incorrect selectors can therefore cause serious problems.

---

# 59. Namespaces

Kubernetes namespaces provide logical separation within a cluster.

For example:

```
Cluster
├── development
├── staging
└── production
```

Each namespace can contain resources such as:

```
Pods
Deployments
Services
ConfigMaps
Secrets
```

Namespaces are useful for organization and access control.

They are not equivalent to completely separate clusters and should not be treated as a complete security boundary in every context.

---

# 60. Resource Requests and Limits

Kubernetes scheduling works better when workloads declare resource requirements.

For example:

```
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

A request represents the resources the scheduler should consider when placing the Pod.

A limit defines a maximum boundary enforced by the runtime/kernel mechanisms.

Conceptually:

```
Request
→ "I need approximately this much."

Limit
→ "Do not allow me to exceed this boundary."
```

Resource management is essential in multi-tenant clusters.

---

# 61. Kubernetes Networking Model

Kubernetes networking is built around several important ideas.

Pods should generally be able to communicate with one another across the cluster without requiring manual NAT between every Pod.

A simplified model is:

```
Pod A
  │
  │ Pod Network
  │
  ▼
Pod B
```

The actual implementation is provided by a Container Network Interface (CNI) plugin or networking solution.

Common implementations include different technologies and products, but the conceptual Kubernetes networking model remains the important foundation.

---

# 62. Service Load Distribution

A Service provides a stable abstraction over multiple Pods.

For example:

```
Service
  │
  ├── Pod A
  ├── Pod B
  └── Pod C
```

Traffic can be distributed among eligible backend Pods.

This means the client does not need to know:

```
Pod A IP
Pod B IP
Pod C IP
```

The Service handles the abstraction.

---

# 63. Service Endpoints

The Service needs to know which Pods are eligible backends.

If:

```
Service selector:
app=web
```

and three Pods have:

```
app=web
```

they can become Service endpoints when they are ready.

If one Pod fails its readiness check, it may be removed from the set of ready endpoints.

This gives readiness probes an important relationship with service traffic:

```
Pod readiness
      ↓
Service endpoints
      ↓
Traffic routing
```

---

# 64. Kubernetes Configuration Flow

A typical application deployment can involve:

```
Container Image
       ↓
Deployment
       ↓
ReplicaSet
       ↓
Pods
       ↓
Service
       ↓
Clients
```

Configuration and secrets can be supplied through:

```
ConfigMaps
Secrets
Environment variables
Volumes
```

Resource management can be specified through:

```
Requests
Limits
```

Health can be managed through:

```
Readiness probes
Liveness probes
Startup probes
```

This forms the basic vocabulary of Kubernetes application management.

---

# 65. Practical Lab — Kubernetes Cluster

For a beginner laboratory, you can use one of several local Kubernetes environments.

Possible options include:

```
Minikube
kind
A small kubeadm cluster
A managed Kubernetes cluster
```

For learning, a local cluster is usually easiest because it avoids cloud costs and infrastructure complexity.

The exact installation method depends on your operating system and Kubernetes distribution.

---

# 66. Verify kubectl Access

After setting up a laboratory cluster, verify:

```
kubectl cluster-info
```

Then:

```
kubectl get nodes
```

You should see at least one node.

For example:

```
NAME       STATUS   ROLES
minikube   Ready    control-plane
```

The exact output depends on your environment.

The important result is:

```
STATUS = Ready
```

---

# 67. Practical Lab — First Deployment

Create:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:1.27
          ports:
            - containerPort: 80
```

Apply it:

```
kubectl apply -f web.yaml
```

Inspect:

```
kubectl get deployment
kubectl get replicasets
kubectl get pods -o wide
```

Draw the relationship you observe:

```
Deployment
    ↓
ReplicaSet
    ↓
3 Pods
```

---

# 68. Practical Lab — Delete a Pod

Find a Pod:

```
kubectl get pods
```

Delete it:

```
kubectl delete pod <pod-name>
```

Immediately run:

```
kubectl get pods
```

You should observe Kubernetes creating a replacement.

This demonstrates reconciliation.

The important question is:

> Which component ensures that the desired number of Pods exists?

The answer is the ReplicaSet managed by the Deployment.

---

# 69. Practical Lab — Create a Service

Create:

```
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

Apply it:

```
kubectl apply -f web-service.yaml
```

Then:

```
kubectl get service
```

Inspect:

```
kubectl describe service web
```

Check which Pods are selected.

The critical relationship is:

```
Service selector
        ↓
Pod labels
        ↓
Service endpoints
```

---

# 70. Practical Lab — Test Service Discovery

Run a temporary test Pod:

```
kubectl run test-client \
  --image=busybox:1.36 \
  --rm -it \
  --restart=Never \
  -- sh
```

Inside the Pod, test DNS resolution for:

```
web
```

and attempt to reach the Service.

The exact diagnostic utilities available depend on the image.

The objective is to understand:

```
Client Pod
    ↓
Kubernetes DNS
    ↓
Service
    ↓
Ready Pods
```

---

# 71. Practical Lab — Scale the Application

Start with:

```
replicas = 3
```

Scale to five:

```
kubectl scale deployment web --replicas=5
```

Observe:

```
kubectl get pods
```

Then scale down:

```
kubectl scale deployment web --replicas=2
```

Observe again.

Explain why the number of Pods changes without you manually creating or deleting individual containers.

---

# 72. Practical Lab — Rolling Update

Update the image:

```
kubectl set image deployment/web web=nginx:1.28
```

Monitor:

```
kubectl rollout status deployment/web
```

Then inspect:

```
kubectl get replicasets
kubectl get pods
```

You should be able to identify:

```
Old ReplicaSet
New ReplicaSet
```

Explain how Kubernetes moved from version 1.27 to 1.28.

---

# 73. Practical Lab — Inspect Revision History

Run:

```
kubectl rollout history deployment/web
```

Record the available revisions.

Then inspect the Deployment:

```
kubectl describe deployment web
```

Identify the current image version and ReplicaSets.

Your goal is to understand that a Deployment maintains rollout history rather than simply replacing the old configuration with no record.

---

# 74. Practical Lab — Rollback

Perform a test update to another image version.

Then roll it back:

```
kubectl rollout undo deployment/web
```

Monitor:

```
kubectl rollout status deployment/web
```

Then verify:

```
kubectl get deployment web -o yaml
```

and:

```
kubectl get pods
```

Confirm that the application returned to the previous revision.

---

# 75. Practical Lab — Failed Deployment

For a controlled laboratory exercise, intentionally configure a nonexistent image tag:

```
image: nginx:this-tag-does-not-exist
```

Apply the Deployment.

Then inspect:

```
kubectl get pods
```

and:

```
kubectl describe pod <pod-name>
```

Look for events indicating an image-pull failure.

Restore a valid image and monitor the rollout.

This exercise teaches you to diagnose a deployment instead of simply restarting resources repeatedly.

---

# 76. Practical Lab — Readiness Probe

Create a Deployment with a readiness probe.

A simple HTTP readiness probe might look like:

```
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5
```

Deploy it and inspect the Pod status.

Then intentionally make the probe target incorrect.

Observe:

```
Pod running
but
Pod not Ready
```

Then inspect the Service endpoints.

This demonstrates the distinction between:

```
Running
```

and:

```
Ready to receive traffic
```

---

# 77. Practical Lab — Resource Requests and Limits

Add:

```
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

Apply the Deployment.

Then inspect:

```
kubectl describe pod <pod-name>
```

Understand how the resource configuration affects scheduling and runtime behavior.

Do not confuse:

```
CPU request
```

with:

```
CPU limit
```

They have different purposes.

---

# 78. Practical Lab — Observe Scheduling

Create a Deployment with a deliberately large CPU request that cannot fit on the available laboratory nodes.

For example, use a request appropriate to your cluster but intentionally greater than available capacity.

Observe:

```
kubectl get pods
```

You may see:

```
Pending
```

Then:

```
kubectl describe pod <pod-name>
```

Inspect scheduling events.

This demonstrates why resource requests matter to the scheduler.

---

# 79. Troubleshooting Methodology

A good Kubernetes troubleshooting process is systematic.

Start with:

```
kubectl get pods
```

Then identify the problematic resource.

Inspect it:

```
kubectl describe pod <pod-name>
```

Check logs:

```
kubectl logs <pod-name>
```

Inspect the Deployment:

```
kubectl describe deployment <deployment-name>
```

Check Services:

```
kubectl get services
```

Check endpoints and labels.

Then investigate the underlying node if necessary.

The principle is:

```
Observe
→ Identify
→ Inspect
→ Form hypothesis
→ Test
→ Correct
→ Verify
```

Do not randomly delete resources until the problem disappears.

---

# 80. Troubleshooting: Service Has No Backends

Suppose:

```
Service exists
but
application cannot be reached
```

Check:

```
kubectl get endpoints
```

and compare:

```
Service selector
```

with:

```
Pod labels
```

For example:

```
Service:
selector app=web

Pods:
label app=frontend
```

No match means the Service has no appropriate backends.

The solution is not to change networking randomly. Correct the label/selector relationship.

---

# 81. Troubleshooting: Pods Are Running but Service Fails

Suppose:

```
Pods → Running
Service → Exists
```

but requests fail.

Investigate:

```
Pod readiness
Service selector
Service targetPort
Application listening port
Network policy
DNS
Application logs
```

Remember:

```
Running ≠ Ready
```

and:

```
Service port ≠ targetPort
```

These two distinctions solve many beginner Kubernetes networking problems.

---

# 82. Troubleshooting: Deployment Update Stalls

Suppose:

```
kubectl rollout status deployment/web
```

does not complete.

Inspect:

```
kubectl get pods
kubectl get replicasets
kubectl describe deployment web
kubectl describe pod <new-pod>
```

Look for:

```
Image errors
Readiness failures
Insufficient resources
Scheduling problems
Application crashes
Volume problems
```

The rollout controller can only progress if the new Pods become usable according to the Deployment's conditions.

---

# 83. Troubleshooting: Rollback Does Not Solve the Problem

If rollback does not restore service, consider whether the failure is actually related to the application version.

For example:

```
Application v1 → previously healthy
Application v2 → deployed
Database migration → schema changed
Rollback → application v1
Database → schema v2
```

The application can still fail.

Other possible causes include:

```
Infrastructure failure
Node failure
Network failure
Persistent data corruption
External dependency failure
Configuration changes
Secret changes
```

Rollback only changes the resources included in the Deployment revision.

---

# 84. Kubernetes and Persistent Applications

Deployments are excellent for many stateless applications.

Stateful workloads require additional considerations.

A database needs:

```
Persistent storage
Stable identity
Careful update strategy
Backup
Recovery
Replication
Consistency
```

Kubernetes provides resources such as StatefulSets and PersistentVolumes for more advanced stateful workloads.

These topics are important but go beyond the main focus of this introductory chapter.

The key lesson is:

> Replicating an application process is not the same as safely replicating application state.

---

# 85. ConfigMaps and Secrets

Applications often need configuration that should not be embedded directly into the image.

A ConfigMap can provide non-sensitive configuration.

A Secret is intended for sensitive values such as credentials or tokens.

Conceptually:

```
Container Image
      +
ConfigMap
      +
Secret
      ↓
Application Configuration
```

This separation makes the image reusable across environments.

For example:

```
Same image
   ↓
Development configuration
```

and:

```
Same image
   ↓
Production configuration
```

---

# 86. Deployment Strategy and Application Design

A successful rolling update requires the application to be designed for rolling updates.

For example, a Web service should ideally:

```
Start quickly
Expose readiness
Handle graceful termination
Avoid local state
Support multiple instances
Use compatible configuration
```

If the application stores important state only in local container memory, replacing the Pod can destroy that state.

Good orchestration therefore depends on good application architecture.

---

# 87. Replica Placement and Availability

Suppose you have three replicas:

```
Pod A → Node 1
Pod B → Node 1
Pod C → Node 1
```

If Node 1 fails:

```
All replicas lost
```

Three replicas do not automatically mean high availability if they are all placed on the same failure domain.

For higher availability, Kubernetes supports mechanisms such as:

```
Pod anti-affinity
Topology spread constraints
Node affinity
```

These can encourage workloads to be distributed across nodes or zones.

---

# 88. Orchestration and Failure Domains

Always ask:

```
What can fail?
```

Possible failure domains include:

```
Container
Pod
Node
Rack
Availability zone
Network segment
Storage system
Control plane
```

An application with three replicas distributed across three nodes has a different availability profile from three replicas on one node.

This is a systems-engineering principle:

> Redundancy is meaningful only when replicas are separated across relevant failure domains.

---

# 89. Kubernetes Updates Are Reconciliation Processes

A useful mental model for updates is:

```
Current State:
v1 × 3

Desired State:
v2 × 3
```

The Deployment controller does not simply execute a fixed script.

It continuously observes:

```
Current replicas
Current revision
Readiness
Availability
Update limits
```

and moves the system toward:

```
v2 × 3
```

This is another example of Kubernetes' reconciliation architecture.

---

# 90. Why Rollbacks Work

A Deployment's revision history allows Kubernetes to retain previous versions of its workload configuration.

Conceptually:

```
Revision 1
   ↓
Revision 2
   ↓
Revision 3
```

A rollback changes the desired state back toward a previous revision.

The controller then reconciles the cluster:

```
Current:
v3

Desired:
v2

Controller:
Create / scale v2
Reduce / remove v3
```

Therefore, rollback is itself another reconciliation process.

---

# 91. Update Safety

Before updating a production application, consider:

```
Is the image immutable or uniquely tagged?
Are readiness probes correct?
Are resource requests realistic?
Is there sufficient capacity?
Is the database migration compatible?
Is the rollback path tested?
Are logs available?
Are metrics available?
Is the change reversible?
```

Avoid relying solely on:

```
latest
```

for production image management.

Explicit versioning makes deployments more predictable.

For example:

```
myapp:2026.08.04
```

is generally easier to reason about than:

```
myapp:latest
```

---

# 92. Canary Deployments

A rolling update gradually replaces replicas.

A canary deployment goes further by exposing a small portion of traffic to the new version before fully replacing the old version.

Conceptually:

```
90% → v1
10% → v2
```

If v2 behaves correctly:

```
50% → v1
50% → v2
```

and eventually:

```
100% → v2
```

Canary deployments require additional routing and observability mechanisms and are beyond the basic Deployment workflow, but understanding the concept is important.

---

# 93. Blue-Green Deployments

Another deployment strategy is blue-green deployment.

You maintain two application environments:

```
Blue → Current version
Green → New version
```

Traffic initially goes to Blue.

After validating Green:

```
Traffic
   ↓
Green
```

The old Blue environment can remain available for a period of time.

This can make rollback extremely fast:

```
Traffic
   ↓
Blue
```

Again, this is a deployment strategy rather than a basic requirement of Kubernetes.

---

# 94. Rolling Update Versus Blue-Green

A rolling update:

```
Gradually replace Pods
```

A blue-green deployment:

```
Maintain two complete versions
Switch traffic between them
```

Rolling updates generally use less duplicate capacity.

Blue-green deployments can provide very fast traffic switching and rollback but may temporarily require substantially more resources.

The appropriate strategy depends on:

```
Application
Capacity
Risk
Rollback requirements
Database compatibility
Traffic architecture
```

---

# 95. Kubernetes Operational Commands

The following commands are worth becoming comfortable with:

```
kubectl get
kubectl describe
kubectl logs
kubectl exec
kubectl apply
kubectl delete
kubectl scale
kubectl rollout status
kubectl rollout history
kubectl rollout undo
```

You should not memorize them as isolated commands.

Understand what each category does:

```
get
→ Observe

describe
→ Investigate

logs
→ Diagnose application behavior

exec
→ Enter a running container for inspection

apply
→ Declare desired configuration

scale
→ Change replica count

rollout
→ Manage Deployment revisions
```

---

# 96. A Complete Application Flow

A simple production-style Kubernetes application can be visualized as:

```
                         User
                          │
                          ▼
                    External Entry
                          │
                          ▼
                       Service
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
           Pod A        Pod B        Pod C
             │            │            │
             ▼            ▼            ▼
         Container    Container    Container
             │            │            │
             └────────────┼────────────┘
                          ▼
                    Application
```

The Deployment manages the replicas:

```
Deployment
     ↓
ReplicaSet
     ↓
Pods A/B/C
```

The Service provides stable access:

```
Service
     ↓
Ready Pods
```

The control plane manages the desired state:

```
API Server
     ↓
Controllers
     ↓
Scheduler
     ↓
Worker Nodes
```

This architecture is the foundation of Kubernetes application management.

---

# 97. Complete Practical Exercise — Deploy, Scale, Update, Roll Back

This exercise combines the main concepts from the chapter.

## Step 1 — Deploy version 1

Create a Deployment with:

```
Name: web
Replicas: 3
Image: nginx:1.27
```

Apply it.

Verify:

```
kubectl get deployment
kubectl get pods
```

## Step 2 — Expose the application

Create a ClusterIP Service:

```
Service name: web
Port: 80
Target port: 80
Selector: app=web
```

Verify:

```
kubectl get service
```

## Step 3 — Scale

Scale to five replicas.

Verify:

```
kubectl get pods
```

## Step 4 — Update

Change the image to:

```
nginx:1.28
```

Monitor:

```
kubectl rollout status deployment/web
```

## Step 5 — Inspect the revision

Run:

```
kubectl rollout history deployment/web
```

## Step 6 — Simulate a failed deployment

Change the image to an invalid tag.

Observe:

```
kubectl get pods
kubectl describe pod <pod-name>
```

## Step 7 — Roll back

Run:

```
kubectl rollout undo deployment/web
```

Verify:

```
kubectl rollout status deployment/web
```

## Step 8 — Explain the system

Without looking at your notes, explain:

```
Deployment
    ↓
ReplicaSet
    ↓
Pods
    ↓
Containers

Service
    ↓
Ready Pods
```

If you can explain this architecture clearly, you understand the central Kubernetes workflow.

---

# 98. Advanced Exercise — Update Policy

Create a Deployment with:

```
replicas: 5
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 2
```

Predict the maximum and minimum number of Pods that can exist during a rolling update.

Then perform an update and observe what actually happens.

Explain:

```
Why were extra Pods created?
Why were old Pods removed?
When did a new Pod become eligible to receive traffic?
What would happen if readiness failed?
```

This exercise transforms abstract update parameters into operational understanding.

---

# 99. Advanced Exercise — Readiness Failure During Update

Create a valid Deployment.

Then modify the readiness probe so that the new version cannot become ready.

Start a rolling update.

Observe:

```
kubectl rollout status deployment/web
```

Then inspect:

```
kubectl get pods
kubectl get replicasets
kubectl describe deployment web
```

Explain why Kubernetes does not immediately remove all old Pods.

This demonstrates why readiness is fundamental to safe rolling updates.

---

# 100. Advanced Exercise — Rollback Reasoning

Create:

```
Version 1 → working
Version 2 → working
Version 3 → broken
```

Deploy all three versions sequentially.

Then inspect the revision history.

Rollback to version 2.

Then answer:

```
Which ReplicaSet represents version 2?
Which ReplicaSet represents version 3?
Why does the Deployment know how to return to version 2?
What application data is not automatically restored?
```

The last question is especially important.

---

# 101. Common Beginner Misconceptions

### "Kubernetes runs containers directly."

Not exactly.

Kubernetes orchestrates workloads. A container runtime executes the containers.

### "A Pod is the same thing as a container."

No.

A Pod is the smallest deployable Kubernetes unit and may contain one or more containers.

### "A Deployment is a container."

No.

A Deployment manages ReplicaSets and desired application state.

### "A Service is the application."

No.

A Service provides stable network access to selected Pods.

### "Three replicas guarantee high availability."

Not automatically.

If all three replicas are on one failed node, all three can disappear together.

### "Rollback restores everything."

No.

Rollback primarily restores a previous workload configuration. It does not automatically restore arbitrary persistent application data.

---

# 102. Kubernetes Versus Docker

It is important not to confuse Kubernetes with Docker.

Docker is primarily a container platform and ecosystem.

Kubernetes is a container orchestration platform.

A simplified relationship is:

```
Kubernetes
    ↓
Orchestration
    ↓
Container Runtime
    ↓
Containers
```

Modern Kubernetes clusters often use containerd or CRI-O rather than Docker Engine as the runtime.

Docker images, however, are widely used because modern container ecosystems are based on standardized image formats and interfaces.

---

# 103. Kubernetes Versus Virtualization

Kubernetes does not replace virtualization in every environment.

A common infrastructure stack is:

```
Physical Hardware
       ↓
Hypervisor
       ↓
Virtual Machines
       ↓
Kubernetes Worker Nodes
       ↓
Pods
       ↓
Containers
```

This combines:

```
Virtualization
→ Hardware/OS isolation

Containers
→ Application isolation

Kubernetes
→ Cluster orchestration
```

These technologies solve different layers of the infrastructure problem.

---

# 104. Kubernetes and Resource Efficiency

Kubernetes can improve infrastructure utilization by scheduling workloads across available resources.

Suppose:

```
Node A
CPU utilization: 20%

Node B
CPU utilization: 80%
```

A scheduler can place new workloads where they fit according to requests and constraints.

However, Kubernetes does not magically create resources.

If the cluster has insufficient capacity:

```
Requested resources > Available resources
```

Pods can remain pending.

Good capacity planning is therefore still necessary.

---

# 105. Observability

Production Kubernetes administration requires observability.

At minimum, administrators should be able to answer:

```
Is the application available?
How many replicas are ready?
Are Pods restarting?
Are nodes healthy?
Is CPU saturated?
Is memory exhausted?
Are requests failing?
Did a deployment recently change?
```

Useful signals include:

```
Logs
Metrics
Events
Traces
Health probes
```

`kubectl` is excellent for interactive troubleshooting, but production environments generally need dedicated monitoring and logging systems.

---

# 106. Event-Driven Troubleshooting

Kubernetes Events often explain why an operation failed.

For example:

```
kubectl describe pod <pod-name>
```

may reveal events such as:

```
Scheduled
Pulled
Created
Started
Failed
Back-off
Unhealthy
```

These events provide a timeline.

When troubleshooting, reconstruct the sequence:

```
What did Kubernetes attempt?
What succeeded?
What failed?
What happened immediately before the failure?
```

This is much more effective than guessing.

---

# 107. Production Update Checklist

Before performing an important Deployment update, verify:

```
Image tag is correct
Image exists
Image provenance is trusted
Enough cluster capacity exists
Readiness probe works
Liveness probe is appropriate
Resource requests are realistic
Resource limits are reasonable
Update strategy is appropriate
Rollback history is available
Application logs are accessible
Database compatibility has been considered
Persistent data is backed up
```

The update should be treated as an operational change, not simply a command.

---

# 108. Production Rollback Checklist

When considering rollback:

```
Identify the failing revision
Confirm the previous revision was healthy
Check whether database schema changed
Check configuration changes
Check Secrets and ConfigMaps
Check external dependencies
Check persistent data
Execute rollback
Monitor rollout
Verify application functionality
Investigate the original failure
```

A rollback is a recovery action, not a substitute for root-cause analysis.

---

# 109. The Kubernetes Reconciliation Loop

The most important conceptual model in this chapter is the reconciliation loop.

It can be visualized as:

```
              Desired State
                    │
                    ▼
              Kubernetes API
                    │
                    ▼
               Controllers
                    │
                    ▼
              Actual Cluster
                    │
                    ▼
               Observe State
                    │
                    └───────────┐
                                │
                                ▼
                         Reconcile Again
```

This loop continues continuously.

If a Pod disappears, Kubernetes notices.

If the desired replica count changes, Kubernetes notices.

If the application image changes, Kubernetes notices.

If a new node becomes available, scheduling decisions can change.

Kubernetes is therefore not merely executing a deployment script. It is operating a control system.

---

# 110. The Four Core Objects to Remember

For this chapter, remember these four Kubernetes objects:

```
Pod
```

The smallest deployable unit containing one or more containers.

```
Deployment
```

The desired-state controller used commonly for stateless applications.

```
ReplicaSet
```

The controller that maintains the requested number of Pod replicas.

```
Service
```

The stable networking abstraction that directs traffic toward selected Pods.

Their relationship is:

```
Deployment
     ↓
ReplicaSet
     ↓
Pods
     ↓
Containers

Service
     ↓
Selected / Ready Pods
```

If this diagram becomes intuitive, much of beginner Kubernetes becomes easier.

---

# 111. Final Synthesis

Container orchestration exists because managing individual containers manually does not scale.

A container runtime can answer:

> How do I run this container?

An orchestrator answers a much larger set of questions:

```
Where should workloads run?
How many replicas should exist?
What happens when one fails?
How do clients reach the application?
How do we update it safely?
How do we undo a failed update?
How do we distribute workloads across machines?
```

Kubernetes addresses these problems using a declarative, API-driven architecture.

The control plane stores and manages desired state.

The scheduler decides where Pods should run.

The kubelet ensures that assigned Pods are running on worker nodes.

The container runtime executes the actual containers.

A Deployment manages application revisions and ReplicaSets.

A ReplicaSet maintains the requested number of Pods.

A Service provides stable networking to a changing set of Pods.

Rolling updates allow Kubernetes to transition gradually from one application revision to another.

Rollbacks allow the Deployment to return to a previous revision when a new version is defective.

The essential architecture is:

```
                         CONTROL PLANE
                              │
                  ┌───────────┼───────────┐
                  ▼           ▼           ▼
              API Server  Scheduler  Controllers
                  │
                  ▼
                 etcd
                  │
                  ▼
              Desired State
                  │
                  ▼
        ┌─────────────────────────┐
        │       Worker Nodes      │
        │                         │
        │ kubelet + runtime       │
        │                         │
        │  Pod   Pod   Pod        │
        │   │     │     │        │
        │  App   App   App       │
        └─────────────────────────┘
                  ▲
                  │
               Service
                  ▲
                  │
                Client
```

And the update model is:

```
Stable v1
   │
   ▼
Desired v2
   │
   ▼
New ReplicaSet
   │
   ▼
New Pods become Ready
   │
   ▼
Old Pods gradually removed
   │
   ▼
Stable v2
```

If the update fails:

```
v2
 ↓
Failure
 ↓
kubectl rollout undo
 ↓
Previous revision
 ↓
Reconciliation
 ↓
Stable application
```

The most important principle to retain is:

> **Kubernetes does not primarily manage individual containers; it manages desired application state and continuously reconciles the cluster toward that state.**

---

# 112. Knowledge Check

Before moving to the next chapter, make sure you can answer these questions without consulting your notes.

## Fundamentals

1. What problem does container orchestration solve?
    
2. Why does managing containers manually become difficult at scale?
    
3. What does "desired state" mean?
    
4. What is declarative infrastructure management?
    
5. How is declarative management different from imperative management?
    

## Kubernetes Architecture

6. What is Kubernetes?
    
7. What is the role of the control plane?
    
8. What does the Kubernetes API server do?
    
9. What is etcd used for?
    
10. What does the scheduler do?
    
11. What is a worker node?
    
12. What does kubelet do?
    
13. What is a container runtime?
    
14. Why is Kubernetes not the same thing as Docker?
    

## Pods and Workloads

15. What is a Pod?
    
16. Why does Kubernetes schedule Pods rather than individual containers?
    
17. Can a Pod contain more than one container?
    
18. Why are Pods considered ephemeral?
    
19. What is a Deployment?
    
20. What is a ReplicaSet?
    
21. What is the relationship between a Deployment, ReplicaSet, and Pods?
    

## Services

22. Why should applications generally not depend on individual Pod IP addresses?
    
23. What is a Kubernetes Service?
    
24. What is a Service selector?
    
25. How do Pod labels relate to Service selectors?
    
26. What is the difference between `port` and `targetPort`?
    
27. What is a ClusterIP Service?
    
28. What is Kubernetes service discovery?
    
29. Why are readiness probes important to Services?
    

## Replicas and Scaling

30. What does `replicas: 3` mean?
    
31. What happens if one of three Pods managed by a ReplicaSet fails?
    
32. How can a Deployment be scaled?
    
33. Why do multiple replicas improve availability?
    
34. Why do multiple replicas not automatically guarantee high availability?
    
35. What is a failure domain?
    

## Updates

36. What is a rolling update?
    
37. Why are rolling updates useful?
    
38. What does `maxUnavailable` control?
    
39. What does `maxSurge` control?
    
40. What is a Deployment revision?
    
41. How can you inspect rollout history?
    
42. How can you monitor an active rollout?
    

## Rollbacks

43. What is a rollback?
    
44. How do you perform a basic Deployment rollback?
    
45. Why should you identify a known-good revision before rolling back?
    
46. Why is a Kubernetes rollback not the same thing as restoring a backup?
    
47. Why can database schema changes make rollback difficult?
    

## Troubleshooting

48. What does `CrashLoopBackOff` generally indicate?
    
49. What can cause a Pod to remain `Pending`?
    
50. What does `ImagePullBackOff` generally indicate?
    
51. What should you inspect when a Service has no working backends?
    
52. What is the difference between a Pod being `Running` and being `Ready`?
    
53. Which commands would you use to investigate a failing Pod?
    
54. Why are Kubernetes Events useful?
    

## Architecture

55. Explain the complete relationship:
    

```
Deployment
    ↓
ReplicaSet
    ↓
Pod
    ↓
Container
```

56. Explain the relationship:
    

```
Service
    ↓
Selector
    ↓
Ready Pods
```

57. Explain how Kubernetes performs a rolling update.
    
58. Explain how Kubernetes performs a rollback.
    
59. Explain the Kubernetes reconciliation loop in your own words.
    
60. Explain why Kubernetes, container runtimes, and hypervisors solve different problems.
    

---

# 113. Final Mental Model

If you remember only one model from this chapter, remember this:

```
                         USER / ADMINISTRATOR
                                  │
                                  ▼
                              kubectl
                                  │
                                  ▼
                           KUBERNETES API
                                  │
                 ┌────────────────┼────────────────┐
                 ▼                ▼                ▼
             Scheduler       Controllers         etcd
                 │                │                │
                 └────────────────┼────────────────┘
                                  │
                           Desired State
                                  │
                                  ▼
                         WORKER NODES
                                  │
                 ┌────────────────┼────────────────┐
                 ▼                ▼                ▼
               Pod A            Pod B            Pod C
                 │                │                │
                 ▼                ▼                ▼
             Container        Container        Container
                 │                │                │
                 └────────────────┼────────────────┘
                                  ▼
                               SERVICE
                                  │
                                  ▼
                                CLIENT
```

For updates:

```
             Deployment v1
                    │
                    ▼
             Stable Replicas
                    │
             Change Image
                    │
                    ▼
             Deployment v2
                    │
                    ▼
             New ReplicaSet
                    │
                    ▼
          New Pods become Ready
                    │
                    ▼
          Old Pods are removed
                    │
                    ▼
             Stable v2
```

For failure:

```
Pod fails
   ↓
ReplicaSet detects fewer replicas
   ↓
Replacement Pod created
   ↓
Scheduler selects a suitable node
   ↓
kubelet starts the Pod
   ↓
Readiness succeeds
   ↓
Service can route traffic
```

For rollback:

```
v2 deployed
    ↓
Application failure
    ↓
Identify known-good revision
    ↓
Rollback Deployment
    ↓
Desired state returns to v1
    ↓
Kubernetes reconciles
    ↓
v1 Pods become active
```

The central idea is:

> **Container runtimes run containers. Kubernetes orchestrates workloads. Deployments manage application revisions. ReplicaSets maintain replica counts. Pods are the units Kubernetes schedules. Services provide stable access to dynamic Pods. Rolling updates change versions progressively, while rollbacks restore a previous workload revision.**