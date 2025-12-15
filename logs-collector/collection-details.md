# Collection Details

## Overview
This document provides transparency about the output collected when running the logs collector script. The collection is designed to gather necessary troubleshooting information while respecting privacy and security concerns

Where possible output from the collection is sanitized, however we recommend you check a log collection and remove or edit any sensitive data

### Node-level collection

Output that is collected only from the node where the logs collector script is run

#### Operating System
- General OS configuration, for example: the hostname, resources, process list, service list, packages, limits and tunables
- Journalctl output for related services if available, a list of services is listed in [the `JOURNALD_LOGS` variable](https://github.com/supporttools/scripts/blob/main/logs-collector/rancher2_logs_collector.sh#L12)
- OS logs from /var/logs, a list of log files is listed in [the `VAR_LOG_FILES` variable](https://github.com/supporttools/scripts/blob/main/logs-collector/rancher2_logs_collector.sh#L15)

#### Networking
- **Firewall rules**: iptables (filter, nat, mangle, raw tables), ip6tables, nftables ruleset, firewalld zones and rules
- **IP configuration**: addresses, routes, routing rules, neighbors (ARP/NDP), link statistics (IPv4 and IPv6)
- **Multicast**: multicast group memberships (`ip maddr`)
- **Socket statistics**: TCP, UDP, Unix, raw sockets via `ss` and `netstat`
- **Network namespaces**: list of namespaces, namespace directory contents
- **Bridge/VLAN**: bridge links, forwarding database (FDB), VLAN configuration
- **Per-interface data**: FDB and neighbor cache for CNI overlay interfaces (flannel.1, vxlan.calico, cilium_vxlan, cni0, docker0)
- **DNS resolution**: systemd-resolved status and configuration (`resolvectl`, `/etc/systemd/resolved.conf`)
- **systemd-networkd**: network controller status and configuration files
- **Interface details**: ethtool output for all interfaces (general info, driver, features)
- **Kernel tunables**: filtered `sysctl` output for `net.*` parameters
- **Load balancing**: IPVS rules and statistics
- **CNI configuration**: files from `/etc/cni/net.d/`
- **Calico** (if calicoctl available): node status, nodes, IP pools, BGP configuration
- **Cilium** (if cilium CLI available): status, BPF endpoint list, BPF load balancer list

#### Kubernetes
- Distribution logs, for example rke2 and k3s agent/server journalctl logs
- Distribution configuration, rke2 and k3s configuration files, static pod manifests
- Container runtime logs and configuration, containerd or docker

### Cluster-level collection

Output that is collected from the cluster

Note, pod logs from other nodes and additional kubectl output can only be collected when running on a control plane/server node

#### Kubernetes
- Kubernetes control plane and worker component configuration and logs, for example: kubelet etcd, kube-apiserver
- Kubernetes pod logs from related namespaces, a list of namespaces is listed in [the `SYSTEM_NAMESPACES` variable](https://github.com/supporttools/scripts/blob/main/logs-collector/rancher2_logs_collector.sh#L6) located in the script
- Directory listings, for example: rke2 manifests directory, SSL certificates, etcd snapshots

#### Kubectl output
- Kubectl list of nodes, pods, services, RBAC roles, persistent volumes, events, ingress and deployments
- Cluster provisioning CRD objects