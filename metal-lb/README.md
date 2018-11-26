## Metal LB
Metal Load Balancer is a Network Load Balancer implementation for bare-metal Kubernetes clusters.
https://metallb.universe.tf/

## Guide

1. Run minikube

```bash
minikube start
minikube ip
# 192.168.99.100
```

2. Deploy `metal-lb`

```bash
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml

kubectl get pods -n metallb-system
# NAME                          READY   STATUS    RESTARTS   AGE
# controller-7fbd769fcc-9g44h   1/1     Running   0          1m
# speaker-wfd9p                 1/1     Running   0          1m
```

3. View, Edit and then Deploy `metal-lb-config`

```bash
cat metal-lb-config.yaml
vim metal-lb-config.yaml

kubectl apply -f metal-lb-config.yaml
kubectl logs -l component=speaker -n metallb-system
```

4. Deploy example

```bash
kubectl apply -f example-pod-whoami.yaml
kubectl apply -f example-http-whoami.yaml
```

5. Test `example-pod-whoami`

```bash
kubectl get svc example-pod-whoami
# NAME                 TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
# example-pod-whoami   LoadBalancer   10.99.2.216   192.168.99.244   80:30629/TCP   28s

curl 192.168.99.244
# I'm example-pod-whoami-5d6cbfd995-kzsst

curl 192.168.99.244
# I'm example-pod-whoami-5d6cbfd995-whsh8
```

6. Test `example-http-whoami`

```bash
kubectl get svc example-http-whoami
# NAME                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
# example-http-whoami   LoadBalancer   10.99.26.204   192.168.99.243   80:31185/TCP   5m

curl 192.168.99.244
# Hostname: example-http-whoami-8487478655-c8gng
# IP: 127.0.0.1
# IP: 172.17.0.15
# GET / HTTP/1.1
# Host: 192.168.99.243
# User-Agent: curl/7.62.0
# Accept: */*

curl 192.168.99.244
# Hostname: example-http-whoami-8487478655-fmvhk
# IP: 127.0.0.1
# IP: 172.17.0.11
# GET / HTTP/1.1
# Host: 192.168.99.243
# User-Agent: curl/7.62.0
# Accept: */*
```
