# scripts

This repository contains support tools maintained by **Mattox Engineering** to assist with investigating and troubleshooting issues with Kubernetes clusters, as well as other maintenance tasks.

## Caution

This repository contains scripts that can cause harm if used without proper guidance. We advise reaching out to Mattox Engineering support before executing any of these scripts in production environments. Failure to do so could incur production downtime.

## Repository Structure

The repository consists of the following directories:

- **logs-collector**: Non-mutating, non-destructive scripts for collecting diagnostic information and logs from Kubernetes nodes. Supports RKE2, RKE1, K3s, and kubeadm clusters.

## Quick Start

### Logs Collector

Download and run the logs collector on a Kubernetes node:

```bash
curl -OLs https://raw.githubusercontent.com/supporttools/scripts/main/logs-collector/rancher2_logs_collector.sh
sudo bash rancher2_logs_collector.sh
```

See [logs-collector/README.md](logs-collector/README.md) for detailed usage and options.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
