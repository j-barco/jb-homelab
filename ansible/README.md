# Ansible

Configuration management for every machine in the homelab (Proxmox host, VMs, LXC containers, Raspberry Pi).

```
ansible/
├── inventory/   # Hosts and groups
├── roles/       # common, k3s_server, k3s_agent, ...
└── playbooks/   # Entry points
```

Planned first roles: `common` (users, SSH hardening, updates), `k3s_server`, `k3s_agent`.

Secrets must be encrypted (Ansible Vault or SOPS) and never committed in plain text.
