# Intro

## OS-Level virtualisation

Containers are lightweight because they remove:

- Cost of virtualisation (do not manage hardware)
- Cost of running multiple OS (there is only one OS)
- Cose of the blackbox (the single OS decides and sees all)

## History

- `1982` | `chroot` | UNIX filesystem isolation only
- `2000` | `FreeBSD jail` | First containers
- `2005` | `OpenVZ` | Patched Linux Kernel
- `2008` | `LXC` | Mainline Linux Kernel
- `2013` | `Docker` | runc (Open Container Initialive (OCI), Portability across OS)

LXC and Docker rely on the kernel isolation features:

## Kernel isolation features

### Linux CGroups (Resource / perf)
- Linux kernel feature that limits, accounts for, and isolates the resource usage of a collection of processes 
- Some examples: 
    - `cpu`
    - `cpuacct` 
    - `cpuset`
    - `memory`
    - `blkio`
    - `freezer`
    - `devices`
    - `pids`
    - `hugetlb`
    - `net_prio`
    - `net_cls`
    - `perf_event`

CGroup-v1

```bash
for R in cpu memory blkio ...
do
    CGPATH = "/sys/fs/cgroup/${R}/${CG}"

    # Create cgroup
    mkdir "${CGPATH}"

    # Add self to cgroup
    echo $$ > "${CGPATH}/tasks"
done 

exec_to_isolate
```

### CFS Quota

Deux fichiers speciaux

- `cpu.cfs_period_us`
- `cpu.cfs_quota_us`

On peut limiter le nombre de cycles de CPU par `cfs`.

### Linux namespaces

> Kernel feature that create a private local view of sysstem resources. Resources outsite the namespace are invisible. Resources inside childres Namespace are visible but do not have the same name.

| Namespaces | Isolates |
| --- | --- |
| Cgroup | Cgroup root directory |
| IPC | SysV IPC, POSIX message queues |
| Network | Network devices, stacks, ports, etc |
| Mount | Mount points |
| PID | Process IDs |
| Users | User and group IDs |
| UTS | Hostname and NIS domain name |

## Linux security

Can be applied to container

| Feature | Desc |
| --- | --- |
| `AppArmor` | Define for each applications what can be accessed and with what privileges |
| `seccomp` | restrains system to call only `exit` `sigreturn` `read` and `write` |
| `linux capabilities` | avoids all root privileges when only a subset is required |
| `ulimit` | limits max number of opened files, processces... |

## Memory isolation

### Quantitative

> With a good quantitative isolation, `cgroups` should not have more pages than their limit
    
`cgroups` never exceed their limit because whenever their demand a new page `try_charge` is called

### Qualitative

> With a good qualitative isolation, `cgroups` should acces their data in memory as if they were alone on the machine

Linux keeps track of the utility of pages with a set of lists `lru_list`. When memory is running out, the least recently used pages are reclaimed. With a single list, the pages of other `cgroups` have to be filtered.

With additional local lists the pages of one `cgroups` can be reclaimed. But there are still lock interferences between `cgroups`.

#### Qualitative Memory Isolation

With local lists only, the isolation is very good, but global recency is lost.

# Docker

## Image layers


- Good at storing the initial state of container
- bad at storing data for IO heavy applications
- Updates in the C_Layer (CopyOnWrite has an overhead on first write)
- C_layer lost when the container is removed

Sharing layers avoids duplication
- Saves storage
- Speeds boot up time of new instances

## Volumes

- Are independent of the running container
- Can be shared among containers

## Networking

Docker uses Network Namespaces combined with:

- Virtual Ethernet Bridges (`docker0` or `br-XXX`)
- Virtual Ethernet Devices (`vethXXX` in host, eth)
- Packet Forwarding (`sysctl net.ipv4.conf.all.forwarding`)
- NAT (`iptables -L`)

## `docker-compose`

```docker
version: "2"
services:
  web:
    build: web
    command: python app.py
    ports:
     - "5000:5000"
    volumes:
     - ./web:/code # modified here to take into account the new app path
    links:
     - redis
    environment:
     - DATADOG_HOST=datadog # used by the web app to initialize the Datadog library
  redis:
    image: redis
  # agent section
  datadog:
    build: datadog
    links:
     - redis # ensures that redis is a host that the container can find
     - web # ensures that the web app can send metrics
    environment:
     - API_KEY=__your_datadog_api_key_here__
    volumes:
     - /var/run/docker.sock:/var/run/docker.sock
     - /proc/:/host/proc/:ro
     - /sys/fs/cgroup:/host/sys/fs/cgroup:ro
```

## `docker-swarm`

When 1 machine is not enough any more. Emulates a swarm of machines as a single one. Ca scale pas, au dela de ~10 machines, chercher autre solution like Kubernetes

## Kubernetes clusters 

> Kubernetes (commonly referred to as "K8s"[3]) is an open-source system for automating deployment, scaling and management of containerized applications[4] that was originally designed by Google and donated to the Cloud Native Computing Foundation. It aims to provide a "platform for automating deployment, scaling, and operations of application containers across clusters of hosts".[5] It supports a range of container tools, including Docker.
>
> [*Wiki*](https://en.wikipedia.org/wiki/Kubernetes)