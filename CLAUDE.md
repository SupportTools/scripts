# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a scripts repository maintained by **Mattox Engineering**, containing diagnostic and troubleshooting tools for Kubernetes environments. The primary tool is a logs collector supporting various Kubernetes distributions (RKE2, RKE1, K3s, kubeadm).

## Repository Structure

```
scripts/
├── logs-collector/
│   ├── rancher2_logs_collector.sh  # Main logs collection script (~1800 lines)
│   ├── README.md                    # Usage documentation
│   └── collection-details.md        # Details on what data is collected
└── LICENSE                          # Apache 2.0
```

## Main Script: rancher2_logs_collector.sh

### Running the Script

```bash
# Basic usage (must be run as root)
sudo bash logs-collector/rancher2_logs_collector.sh

# With custom output directory
sudo bash logs-collector/rancher2_logs_collector.sh -d /var/tmp

# Override auto-detected k8s distribution
sudo bash logs-collector/rancher2_logs_collector.sh -r rke2

# Custom RKE2 data directory
sudo bash logs-collector/rancher2_logs_collector.sh -c /opt/rke2

# Filter logs by date range
sudo bash logs-collector/rancher2_logs_collector.sh -s 7 -e 5  # relative days
sudo bash logs-collector/rancher2_logs_collector.sh -S 2024-01-01 -E 2024-01-07  # absolute dates

# Obfuscate IPs and hostnames
sudo bash logs-collector/rancher2_logs_collector.sh -o
```

### Key Configuration Variables (top of script)

- `SYSTEM_NAMESPACES`: Kubernetes namespaces to collect pod logs from
- `KUBE_CONTAINERS`: Container names to collect logs from (etcd, kube-apiserver, etc.)
- `JOURNALD_LOGS`: Services to collect journald logs for
- `VAR_LOG_FILES`: Log files to collect from /var/log
- `SPACE`: Minimum disk space required (MB)
- `TIMEOUT`: Command timeout in seconds

### Script Architecture

The script follows a sequential collection pattern:
1. `setup()` - Creates temp directory, sets up paths
2. `disk-space()` - Validates available disk space
3. `sherlock()` - Auto-detects OS, k8s distribution (rke/rke2/k3s), and init system
4. `system-all()` - Collects OS-level info (disk, processes, sysctl)
5. `networking()` - Comprehensive network data collection (see below)
6. Distribution-specific functions (`rke()`, `rke2()`, `k3s()`, etc.)
7. `archive()` - Creates tar.gz output in /tmp (or custom directory)

### Networking Collection (`networking()` function)

The `networking()` function collects comprehensive network diagnostics:
- **Firewall**: iptables (all tables including raw), ip6tables, nftables, firewalld
- **IP/Routing**: addresses, routes, rules, neighbors, multicast groups
- **Sockets**: ss/netstat for TCP, UDP, Unix, raw sockets
- **Namespaces**: network namespace list
- **Bridge/VLAN**: bridge links, FDB entries, VLAN config
- **DNS**: systemd-resolved status and configuration
- **systemd-networkd**: networkctl status and configs
- **Interface details**: ethtool for all interfaces (driver, features)
- **Kernel tunables**: filtered sysctl net.* parameters
- **CNI-specific**: Calico (calicoctl), Cilium (cilium CLI) when available

### Supported Kubernetes Distributions

- RKE2 (auto-detected via `rke2` binary)
- K3s (auto-detected via `k3s` binary)
- RKE1 (auto-detected via `docker` binary)
- Kubeadm (manual override required: `-r kubeadm`)

## Development Notes

- Script is designed to run on production nodes with minimal resource impact
- Uses `nice`/`ionice` with lowest priority by default (override with `-p`)
- Output is sanitized where possible, but `-o` flag adds IP/hostname obfuscation
- Collection is intentionally limited to avoid sensitive data
