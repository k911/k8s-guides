# Kubernetes Dashboard
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

## Installation/Upgrading

```bash
helm upgrade --install metrics stable/metrics-server -f ./values.yaml --namespace kube-system
```

## Usage

```bash
kubectl get nodes
kubectl top node NODE_NAME
```