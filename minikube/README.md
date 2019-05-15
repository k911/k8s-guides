# Minikube

Local, single-node Kubernetes cluster

## Requirements

- [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [`minikube`](https://kubernetes.io/docs/tasks/tools/install-minikube/)

## Quick start

```sh
# make sure it is not running
➜ minikube delete

# configure minikube
➜ minikube config set kubernetes-version v1.14.1
➜ minikube config set cpus 4
➜ minikube config set memory 8192

➜ minikube start
😄  minikube v1.0.1 on linux (amd64)
🤹  Downloading Kubernetes v1.14.1 images in the background ...
🔥  Creating kvm2 VM (CPUs=4, Memory=8192MB, Disk=20000MB) ...
2019/05/15 22:49:22 No matching credentials were found, falling back on anonymous
📶  "minikube" IP address is 192.168.39.99
🐳  Configuring Docker as the container runtime ...
🐳  Version of container runtime is 18.06.3-ce
⌛  Waiting for image downloads to complete ...
✨  Preparing Kubernetes environment ...
💾  Downloading kubeadm v1.14.1
💾  Downloading kubelet v1.14.1
🚜  Pulling images required by Kubernetes v1.14.1 ...
🚀  Launching Kubernetes v1.14.1 using kubeadm ... 
⌛  Waiting for pods: apiserver proxy etcd scheduler controller dns
🔑  Configuring cluster permissions ...
🤔  Verifying component health .....
💗  kubectl is now configured to use "minikube"
🏄  Done! Thank you for using minikube!

```

## Notes

- On Linux I recommend usage of [`kvm2` driver](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#kvm2-driver)
- On MacOs I recommend usage of [`docker-for-mac`'s built-in Kubernetes cluster](https://github.com/k911/local-kubernetes-cluster-docker-for-mac) instead of minikube

## Tips

If you want to use locally built Docker images on minikube, you'll have to run bellow command in terminal, priorior to `docker build`:

```sh
➜ eval $(minikube docker-env)
```