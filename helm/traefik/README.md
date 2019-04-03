# Traefik

Traefik is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy.
https://traefik.io/

## Prerequisites

- [Helm](../helm/README.md) for `stable/traefik` chart installation
- (Optional) [Meta LB](../metal-lb/README.md) for `LoadBalancer` service support on bare-metal clusters

## Installation

```bash
helm install stable/traefik --namespace kube-system --name traefik -f values.yaml
```

Accessing (kubectl proxy): 
http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/traefik-dashboard/proxy/dashboard/

## Upgrading

```bash
helm upgrade traefik stable/traefik -f values.yaml
```

## Example

Source: https://docs.traefik.io/user-guide/kubernetes/

1. Deploy some resources to the cluster:

    ```bash
    # cheese deployments
    kubectl apply -f https://raw.githubusercontent.com/containous/traefik/master/examples/k8s/cheese-deployments.yaml

    # cheese services
    kubectl apply -f https://raw.githubusercontent.com/containous/traefik/master/examples/k8s/cheese-services.yaml

    # cheese ingresses
    kubectl apply -f https://raw.githubusercontent.com/containous/traefik/master/examples/k8s/cheese-ingress.yaml
    ```

2. Proxy cluster network and visit Traefik Dashboard

    ```bash
    kubectl proxy
    ```

    Visit: http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/traefik-dashboard/proxy/dashboard/

    You should see some frontends and backends: `stilton.minikube`, `wensleydale.minikube`, `cheddar.minikube`.

    You can also see that each frontend will have defined 2 backends, that means `Treafik` will load balance beetwen pods, but we cannot access yet this adresses, because they are exposed only in k8s-cluster.

3. To access them we have to get `Treafik` service `LoadBalancer` IP (requires [Metal LB](../metal-lb/README.md) for bare-metal clusters like minikube)

    ```bash
    kubectl get svc traefik --namespace kube-system   
    # NAME      TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
    # traefik   LoadBalancer   10.97.70.54   192.168.99.240   80:31740/TCP,443:31601/TCP   10m 
    ```

    In my case `LoadBalancer` service IP is `192.168.99.240`.

4. Access `stilton.minikube` inside cluster via curl:

    ```bash
    curl http://192.168.99.240 -H "Host: stilton.minikube"
    # <html>
    # <head>
    #     <style>
    #     html { 
    #         background: url(./bg.png) no-repeat center center fixed; 
    #         -webkit-background-size: cover;
    #         -moz-background-size: cover;
    #         -o-background-size: cover;
    #         background-size: cover;
    #     }
    # 
    #     h1 {
    #         font-family: Arial, Helvetica, sans-serif;
    #         background: rgba(187, 187, 187, 0.5);
    #         width: 3em;
    #         padding: 0.5em 1em;
    #         margin: 1em;
    #     }
    #     </style>
    # </head>
    # <body>
    #     <h1>Stilton</h1>
    # </body>
    # </html>
    ```

    Even `https` works, because `Traefik` will provide its default certificate:

    ```bash
    curl https://192.168.99.240 -H "Host: cheddar.minikube" -k
    ```

5. We can also modify our `/etc/hosts`, to be able to access these addresses from browser

    ```bash
    echo "192.168.99.240 stilton.minikube cheddar.minikube wensleydale.minikube" | sudo tee -a /etc/hosts
    ```

    Now, you can visit: https://cheddar.minikube

6. To be sure our requests go through `Traefik`, we can check logs:

    ```bash
    kubectl logs deployments/traefik -n kube-system
    ```