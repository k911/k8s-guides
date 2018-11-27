# Helm
Kubernetes package manager

## Installation

```bash
kubectl apply -f ./helm.yaml
helm init --service-account tiller
```

## Upgrading

```bash
helm init --service-account tiller --upgrade
```