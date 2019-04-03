# Traefik

Traefik is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy.
https://traefik.io/

This guide stands for deploying highly available Traefik cluster, with Let's Encrypt provided TLS/SSL certificates.
Example contains informations how to configure AWS Route53 to DNS Challenge and highly available Zookeeper cluster used as Traefik's Key-Value store.

## Prerequisites

- (Recommended) [External DNS](../external-dns/README.md) for automatic Ingress registration in DNS Hosted Zone
- (Optional) [Meta LB](../metal-lb/README.md) for `LoadBalancer` service support on bare-metal clusters

## Installation / Upgrading

```bash
helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
helm repo update

helm upgrade --install traefik-kv-store incubator/zookeeper -f kv-store.yaml --wait
kubectl apply -f traefik-dashboard-basic-auth.yaml
helm upgrade --install traefik stable/traefik -f values.yaml
```

Accessing (kubectl proxy):
http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/traefik-dashboard/proxy/dashboard/

## Example configuration

With Let's Encrypt enabled, using DNS Challenge via AWS Route53 and Zookeeper as KV Store:

1. Copy example [values.example.yaml](./values.example.yaml) file

    ```bash
    cp values.example.yaml values.yaml
    ```

2. Customize values according to this:

    ```yaml
    dashboard:
      domain: "traefik-dashboard.example.com" # your domain for Traefik's dashboard

    acme:
      enabled: true
      persistence:
        enabled: false
      challengeType: "dns-01"
      email: "devops@example.com"

      # recommended to set to `staging: true` for testing !!
      staging: false
      # because you may hit let's encrypt rate limits

      logging: true
      domains:
        enabled: true
        domainsList:
          - main: "*.example.com" # to get wildcard certificates for xxxx.example.com domains
          - sans:
              - "example.com" # to get SAN certificate for example.com

      dnsProvider:
        name: route53
        # Required AWS IAM permissions:
        # https://github.com/xenolf/lego#aws-route-53
        route53:
          AWS_ACCESS_KEY_ID: XXXXXXXXXX # AWS IAM User Access Key
          AWS_SECRET_ACCESS_KEY: XXXXXXXXXX # AWS IAM User Secret Key
          AWS_REGION: eu-west-2 # AWS Region
          AWS_HOSTED_ZONE_ID: XXXXXXXXXX # AWS Route53 hosted zone to get acme certificates
    ```

3. Review [values.yaml](./values.yaml) file

## Example usage

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
