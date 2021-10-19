# cgroup

What is cgroup applied to? Any schedulable entity (including threads).

Where is cgroup hierarchy represented?
`/sys/fs/cgroup`

Core
- is integrated with eBPF for additional control
- maintains the hierarchy

Controller
- distribute workloads across the hierarchy
- multiple mounts supported

What can be controlled?
- blkio
- cpu
- cpusets: affinity, useful with NUMA
- cpuacct: cpu accounting
- devices: usage and creation, NetFuncVirt, SRIOV
- freezer: suspend execution
- hugetlb: huge pages
- memory
- net_cls: tag sender cgroup, tc (traffic controller)
- net_prio: network priorities per cgroup
- perf_event
- pids: fork/clone limit
- RDMA
- unified

**libcgroup**
- cgset, cgcreate, cgexec

Linux namespaces can be controlled by cgroups.

**What is the problem we are trying to solve?**

- Core workload
- Non-core: monitoring, cron, Chef/Ansible, atop
- Ad-hoc debug: tcpdump, atop

# cgroup v1 vs v2

cgroups per resource vs resources per cgroup.

**v1**
A hierarchy per resource `/sys/fs/cgroup/`
- cpu
- memory
- etc

Each resource hierarchy contains cgroups
for this resource.

Cgroups can be nested.

For each pid we should decide where to it.

Thus, a single pid can be assigned to many cgroups of different resources.

**v2**

A *unified* hierarchy.

A bunch of cgroups at the root `/sys/fs/cgroup/`

Each cgroup controlls all types of resources.

Still, cgroups can be nested.

Why v2? We sacrifice granularity in favor of simplicity.