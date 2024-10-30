# introduction-to-argocd

## Environment Setup

- Install `kind`: https://kind.sigs.k8s.io/docs/user/quick-start/#installation
  ```bash
  brew install kind
  ```
- Install `kubectl`: https://kubernetes.io/docs/tasks/tools/
  ```bash
  brew install kubectl
  ```
- Install `argocd` cli: https://argo-cd.readthedocs.io/en/stable/cli_installation/
  ```bash
  brew install argocd
  ```
- Install `helm`: https://helm.sh/docs/intro/install/
  ```bash
  brew install helm
  ```

## Prepare Local K8s Cluster

- Create cluster:

  ```bash
  kind create cluster
  ```

  Check if pods are running:

  ```bash
  kubectl get pods -A
  ```

- Install ArgoCD: https://argo-cd.readthedocs.io/en/stable/operator-manual/installation/

  ```bash
  kubectl create ns argocd

  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  ```

  Make sure that all pods are running:

  ```bash
  $ kubectl get pods -n argocd
  NAME                                               READY   STATUS    RESTARTS   AGE
  argocd-application-controller-0                    1/1     Running   0          7m9s
  argocd-applicationset-controller-d4df5c8f8-kxld7   1/1     Running   0          7m9s
  argocd-dex-server-5449fdc856-zd7k4                 1/1     Running   0          7m9s
  argocd-notifications-controller-c75bf9cb6-22dt9    1/1     Running   0          7m9s
  argocd-redis-6d5dddc7cb-5sqhd                      1/1     Running   0          7m9s
  argocd-repo-server-cd9d97db-sfnjn                  1/1     Running   0          7m9s
  argocd-server-7755999557-xx2vf                     1/1     Running   0          7m9s
  ```

  Descriptions: `ChatGPT`

  - `argocd-application-controller`: Manages Application state, ensuring synchronization between desired and live states.
  - `argocd-applicationset-controller`: Automates creation of multiple ArgoCD Applications using ApplicationSets.
  - `argocd-dex-server`: Handles SSO authentication via OIDC or LDAP for ArgoCD
  - `argocd-notifications-controller`: Sends notifications on application sync or health status changes.
  - `argocd-redis`: Provides in-memory caching and performance support for ArgoCD.
  - `argocd-repo-server`: Fetches and processes Git repository manifests for the Application Controller.
  - `argocd-server`: Provides the ArgoCD API and web interface for user interaction.

- Install Argo Rollout: https://argo-rollouts.readthedocs.io/en/stable/installation/

  ```bash
  kubectl create namespace argo-rollouts

  kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
  ```

  Make sure that all pods are running:

  ```bash
  $ kubectl get pods -n argo-rollouts
  NAME                             READY   STATUS    RESTARTS   AGE
  argo-rollouts-6cc5ff545b-crr2g   1/1     Running   0          43s
  ```

- ArgoCD UI:

  port-forward `argocd-server` at local port:8080, and keep the connection alive:

  ```bash
  kubectl port-forward svc/argocd-server -n argocd 8080:443
  ```

  Open: https://localhost:8080/

  - Username: `admin`

  - Password:

    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```

## Demo

- Create a simple deployment.yaml
- Replace deployment by rollout object
- Use Argo Application to watch any changes on rollout object
- Watch application with another application
