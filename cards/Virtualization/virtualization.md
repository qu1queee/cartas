Q: What is virtualization?
A: A technology that creates an abstraction layer over physical hardware, allowing multiple isolated virtual environments (VMs or containers) to run on a single physical machine.

---

Q: What is a hypervisor?
A: Software that creates and manages virtual machines by abstracting physical hardware resources (CPU, memory, storage) and allocating them to VMs.

---

Q: What is the difference between a Type 1 and Type 2 hypervisor?
A: Type 1 (bare-metal) runs directly on hardware with no host OS (e.g., VMware ESXi, KVM, Hyper-V). Type 2 (hosted) runs on top of a host OS (e.g., VirtualBox, VMware Workstation).

---

Q: What is a Virtual Machine (VM)?
A: A software emulation of a complete computer — including virtual CPU, memory, storage, and network interfaces — running a full OS in isolation from other VMs on the same host.

---

Q: What is the difference between full virtualization and paravirtualization?
A: Full virtualization emulates hardware completely so the guest OS runs unmodified. Paravirtualization requires a modified guest OS that communicates directly with the hypervisor via a special API for better performance.

---

Q: What is hardware-assisted virtualization?
A: CPU features (Intel VT-x, AMD-V) that allow the hypervisor to run guest instructions at near-native speed by handling privileged operations in hardware rather than software emulation.

---

Q: What is a container and how does it differ from a VM?
A: A container packages an application and its dependencies and shares the host OS kernel. Unlike a VM it has no guest OS, making it lighter and faster to start, but with less isolation.

---

Q: What is the role of a container runtime?
A: Software (e.g., containerd, CRI-O) that pulls images, creates container namespaces and cgroups, and manages the container lifecycle on behalf of an orchestrator or CLI.

---

Q: What are Linux namespaces in the context of containers?
A: Kernel features that isolate container views of system resources — pid, network, mount, UTS, IPC, and user — so each container sees its own isolated environment.

---

Q: What are cgroups (control groups)?
A: A Linux kernel mechanism that limits and accounts for resource usage (CPU, memory, disk I/O) for a group of processes — the foundation of resource constraints in containers.

---

Q: What is a VM snapshot?
A: A point-in-time capture of a VM's entire state (memory, disk, CPU registers) that can be used to roll back the VM to that exact state.

---

Q: What is live migration of a VM?
A: Moving a running VM from one physical host to another with minimal or zero downtime by transferring memory pages and state while the VM keeps running.

---

Q: What is a hypervisor escape?
A: A security vulnerability where malicious code running inside a VM breaks out of the isolation boundary and gains access to the hypervisor or other VMs on the same host.

---

Q: What is nested virtualization?
A: Running a hypervisor inside a VM — enabling a VM to itself host other VMs. Used for testing hypervisors or cloud-based CI that needs to run VMs.

---

C: [KVM] (Kernel-based Virtual Machine) is a Type 1 hypervisor built into the Linux kernel, turning Linux itself into the hypervisor — it is the foundation of most major cloud providers.
