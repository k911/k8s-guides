# Elastic Stack (Loggin)
https://github.com/helm/charts/tree/master/stable/elastic-stack

## Installation/Upgrading

```bash
helm upgrade --install es stable/elastic-stack -f ./values.yaml --namespace elastic-stack
```

## Usage

```bash
kubectl get nodes
kubectl top node NODE_NAME
```