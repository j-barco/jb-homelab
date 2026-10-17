# Kubernetes

Manifests managed through GitOps (ArgoCD).

```
kubernetes/
├── bootstrap/        # ArgoCD and the app-of-apps root
├── infrastructure/   # Registry, Vault, MinIO, monitoring, ...
└── apps/             # Personal services (Vaultwarden, Paperless-ngx, ...)
```

The Raspberry Pi worker has 1 GB of RAM: it will be tainted so only light workloads are scheduled on it.
