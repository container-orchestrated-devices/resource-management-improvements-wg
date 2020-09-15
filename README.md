# Improving Container's resource management APIs across multiple CNCF ecosystem projects

Collaboration space for workgroup focusing on improving resource management for containers

## Problem

With adoption of containerization across industry, the need to fine-grained control of resources is increasing. For special workloads, like Network Functions or HPC, there is also need to enforce specific requirements on special treatment of commontly used resources (CPU, caches, memory and others).
- Currently architecture of Kubernetes or Docker implies set of native resources that use set of assumptions and can't express hardware specifics.
- With increase of usage of VM-based runtimes, there is a need to improve container resource parameters propogation between whole stack of orchestration components

## Ideas

- Propose unified architecture to describe resources organized in different topologies
- Empower Runtimes to be ultimate controller on "how" resources are implemented in orchestration
- Simplify Kubelet code:
  - Remove hardware detection bits, trust information provided by Runtimes
  - Remove code that directly interacts with Linux kernel: e.g. direct cgroup manipulations
  - Unify abstract resource management for Linux and Windows
- Improve CRI APIs to pass information up and down about:
  - Resources available and managable by Runtimes
  - Resources known to Kubelet (e.g. Extended resources)
  - Resources requests/limits of containers and Pods (Sandboxes)
- Implement in Runtimes support for Block I/O, Intel RDT and similar resources that can be described with "classes"
- Enable in CRI implementation pluggable and extendable mechanisms that can eforce different resource policies during container lifecycle events

## TODO

Unprioritized initial set of potential tasks based on ideas above

### Runtimes (containerd, CRI-O, ...)
- [ ] [Provide API](https://github.com/container-orchestrated-devices/resource-management-improvements-wg/issues/1) to Kubelet about known to Runtime resources and their topology
- [ ] Provide mechanisms for pluggable policies that can perform container resource tweakings during container lifecycle events
- [ ] Some way of communication into container about available resources (downwards API?)

### Kubernetes
- [ ] Imrpove Kubelet PodResources APIs to expose information about resources known to Kubelet (extended resources, device plugins instances...)
- [ ] Obsolete dockershim to facilitate evolution of CRI APIs
- [ ] New [CRI API](https://github.com/container-orchestrated-devices/resource-management-improvements-wg/issues/1) for fetching available resources from Runtimes (including hotplug scenarios)
- [ ] Pass container names/resources during Pod(Sandbox) Create/Update messages to enable proper resource pre-allocation for VM-based runtimes.
- [ ] Pass all resources requests/limits to container Create/Update messages, to be able to implement full control on Runtime level 
- [ ] Support for "classes" to attache complex resource templates (e.g. Block I/O)
- [ ] Support for "unlimited" resources

### Node Feature Discovery

- [ ] Get from Kubelet information about known resources, allocations and store it in CRDs. Currently planned to be used by scheduler plugin.
- [ ] Use [topology detection library](https://github.com/container-orchestrated-devices/resource-management-improvements-wg/issues/2) to create Node hardware resources CRD instances

### CRI-Resource-Manager
- Extract pieces of the code as Go libraries that can be re-used in other projects
  - [ ] Replace HW/topology detection bits with [the common topology detection library](https://github.com/container-orchestrated-devices/resource-management-improvements-wg/issues/2)
  - [ ] Block I/O classification/configuration/controller
  - [ ] Intel RDT (L3 cache, Memory Badnwidth) classification/configuration/controller
  - [ ] Policy engine

