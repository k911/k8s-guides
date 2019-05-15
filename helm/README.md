# Helm

Kubernetes package manager

## Guides

- [Kubernetes Dashboard](./k8s-dashboard/README.md)
- [Metal LB](./metal-lb/README.md)
- [Metrics Server](./metrics-server/README.md)
- [Traefik Cluster](./traefik/README.md)
- [External DNS](./external-dns/README.md)
- [Harbor](./harbor/README.md)

## Installation

https://docs.helm.sh/using_helm/#quickstart-guide

## RBAC Tiller Installation

```bash
kubectl apply -f ./helm.yaml
helm init --service-account tiller --upgrade
```

## Upgrading

```bash
helm init --service-account tiller --upgrade
```
