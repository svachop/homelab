# My Homelab: A GitOps Journey

Welcome to my personal homelab repository! 🚀

This project represents my journey into **GitOps**, **Kubernetes**, and **Infrastructure as Code**. It serves as the central source of truth for my home server infrastructure, where everything is managed declaratively.

## Core Concepts

- **GitOps**: The state of the cluster is defined in code. Changes are triggered by `git push`.
- **ArgoCD**: The continuous delivery tool that ensures the live cluster matches this repository.
- **App of Apps**: A hierarchical pattern where one root application manages all other applications.
- **Helm**: Applications are packaged and customized using standard Helm charts.

## Repository Structure

```text
/
├── charts/                 # Local Helm charts & wrappers for applications
│   ├── argo-cd/            # CD Tooling
│   ├── external-secrets/   # Secret Management (External Secrets Operator)
│   ├── metallb/            # Bare-metal Load Balancer
│   └── pihole/             # DNS Sinkhole & Ad Blocker
├── declarative/            # ArgoCD Application Definitions
│   ├── app-of-apps/        # Child apps managed by the root app
│   └── root-app/           # The entry point for ArgoCD
├── docs/                   # Documentation & diagrams
└── kubernetes/             # Raw manifests & core infrastructure (e.g., 1Password Connect)
```

## Managed Applications

### Infrastructure & Networking
- **[ArgoCD](https://argo-cd.readthedocs.io/)**: The heart of the GitOps workflow.
- **[MetalLB](https://metallb.universe.tf/)**: Provides LoadBalancer services for the bare-metal cluster.
- **[External Secrets Operator](https://external-secrets.io/)**: Syncs secrets from external providers (Work in Progress).

### Services
- **[Pi-hole](https://pi-hole.net/)**: Network-wide ad blocking and DNS service.

## Experiments & Roadmap

I am currently experimenting with:
- **1Password Connect**: Integrating 1Password with Kubernetes for secure secret management (located in `kubernetes/core/`).
- Expanding the service catalog with more self-hosted tools.

## How It Works

1.  **Provision**: The physical Kubernetes cluster is provisioned manually.
2.  **Bootstrap**: ArgoCD is installed on the cluster.
3.  **Connect**: The `root-app` is applied, pointing to this repository.
4.  **Sync**: ArgoCD takes over, recursively deploying all apps defined in `declarative/app-of-apps/`.

---
*Feel free to explore the code! This is a living project, constantly evolving as I learn new technologies.*