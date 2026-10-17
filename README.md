# jb-homelab

A personal homelab built to learn and demonstrate **DevOps, infrastructure, cloud and networking** skills.
Everything is defined as code (Ansible, Terraform, Kubernetes manifests) and documented, including the problems I ran into along the way.

> Status: **Network foundation** (WIP)

## Goals

- Build a reproducible, GitOps-driven platform on modest hardware.
- Practice real-world tooling: K3s, ArgoCD, Ansible, Terraform, Vault, Prometheus/Grafana.
- Keep an honest engineering journal of what broke and how I fixed it (see [`docs/journal/`](docs/journal/)).

## Hardware

| Machine | Role | Specs |
|---|---|---|
| Lenovo ThinkCentre M910Q | Main server (Proxmox VE) | Intel Core i5-7600T (4C/4T), 8 GB DDR4, 256 GB NVMe |
| Raspberry Pi 3 | K3s worker node | ARM, 1 GB RAM, 100 Mbps Ethernet |
| TP-Link TL-SG608E | Managed switch (802.1Q VLANs) | 8x Gigabit |
| Desktop (Debian Trixie) | Control node (not 24/7) | Ryzen 5 7600X, 32 GB RAM |

## Target architecture

```
GitHub (code)
     |
GitHub Actions (CI/CD)
     |
Container image -> Private registry (Harbor / registry:2)
     |
Terraform provisions infrastructure (LocalStack / AWS free tier)
     |
ArgoCD deploys to K3s automatically (GitOps)
     |
Secrets managed by Vault
     |
Metrics: Prometheus + Grafana     Logs: ELK Stack
     |
Services exposed through Cloudflare Tunnel (HTTPS)
```

A draw.io diagram will live in [`docs/architecture/`](docs/architecture/).

## Network design

No extra hardware: a single physical NIC with **router on a stick**. OPNsense runs as a VM on Proxmox and routes between VLANs; the ISP router is untouched, so the rest of the house is not affected.

| VLAN | Purpose |
|---|---|
| 99 | WAN (uplink to ISP router) |
| 10 | Management (Proxmox, switch, desktop) |
| 20 | Servers (K3s, LXC services) |
| 30 | Clients |

## Repository layout

```
.
├── ansible/        # Configuration management (inventory, roles, playbooks)
├── terraform/      # Infrastructure as code (LocalStack, AWS)
├── kubernetes/     # Manifests: bootstrap, infrastructure, apps
├── docs/
│   ├── architecture/   # Diagrams (draw.io)
│   ├── journal/        # Problems and solutions
│   └── runbooks/       # Operational procedures
└── .github/workflows/  # CI
```

Companion repo (planned): `jb-homelab-demo-app` - sample application that triggers the CI -> registry -> ArgoCD flow.

## Security

This repository is public. Secrets are never committed: `gitleaks` runs as a pre-commit hook and in CI. Encrypted secrets (SOPS + age or Ansible Vault) will be used until HashiCorp Vault is in place.

## License

[MIT](LICENSE)
