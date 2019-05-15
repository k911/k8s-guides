# Harbor

Higly available secure Docker Registry deployment via helm.

Helm Chart: https://github.com/goharbor/harbor-helm

Repository: https://github.com/goharbor/harbor

## Installation

### Minikube

1. Clone harbor helm repository:

    ```sh
    git clone https://github.com/goharbor/harbor-helm
    cd harbor-helm
    git checkout v1.0.1
    ```

2. Install via helm

    ```sh
    helm upgrade --install goharbor ./harbor-helm  -f values-minikube.yaml --namespace goharbor --wait
    ```

3. Open harbor:

    ```sh
    echo "https://$(minikube ip):31443"
    ```

    Info: Allow unsafe certificate

4. Login with credentials:

    ```yaml
    username: admin
    password: HarborAdmin
    ```