# Kubernetes Dashboard
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

## Installation

```bash
helm install stable/kubernetes-dashboard --namespace kube-system --name k8s-dashboard -f ./values.yaml
```

Accessing (kubectl proxy): http://localhost:8001/api/v1/namespaces/kube-system/services/https:k8s-dashboard-kubernetes-dashboard:/proxy/#!/login 

## Upgrading

```bash
helm upgrade k8s-dashboard stable/kubernetes-dashboard -f ./values.yaml
```

## Minikube
If you're using `Minikube`, it deploys kubernetes dashboard automatically.

To access it run:
```bash
minikube dashboard
```