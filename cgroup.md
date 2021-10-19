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

A single process can be controlled by many groups.
`/sys/fs/cgroup/memory/foo/`
- cgroup.procs
- memory.usage_in_bytes

Other defaults to `/sysdefault`.

**libcgroup**
- cgset, cgcreate, cgexec

Linux namespaces can be controlled by cgroups.