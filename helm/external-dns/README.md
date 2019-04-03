# External DNS

Automatically manages domain names in DNS service like Amazon Route53 with Kubernetes Ingressess and Services.

https://github.com/kubernetes-incubator/external-dns

## Installation / Upgrading

```bash
helm upgrade --install external-dns stable/external-dns -f ./values.yaml
```

## Example configuration

For AWS Route53:

1. Copy example values

    ```bash
    cp values.example.yaml values.yaml
    ```

2. Set values like this:

    ```yaml
    provider: aws

    aws:
      accessKey: XXXXXXXXXXXXXXXXX # iam user access key
      secretKey: XXXXXXXXXXXXXXXXX # iam user secret key
      region: eu-west-2 # aws region
      zoneType: public

    domainFilters:
      - example.com # your hosted zone domain to manage
    ```
