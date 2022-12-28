# Usage

## Create a kubernetes cluster
1. Install k3d
2. Create a cluster: `k3d cluster create chainlink-node-cluster --config ./cluster-config.yml`

## Install ArgoCD in the cluster
1. Create the ArgoCD namespace: `kubectl create namespace argocd`
2. Install ArgoCD in its namespace: `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml` 
3. Access The Argo CD API Server:
   1. `kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'`
   2. `kubectl port-forward svc/argocd-server -n argocd 8080:443`
4. Expose the admin password: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo`
5. Login with admin user: `argocd login cd.argoproj.io --core --name admin --password [your-password]`

## PostgreSQL

### Deploy it with kubernetes

1. Create the app: `kubectl apply -f postgresql/app.yaml`

## Chainlink node

1. Create the app: `kubectl apply -f chainlink/app.yaml`

## SealedSecrets

1. Create the app: `kubectl apply -f sealed-controller/app.yaml`
2. Fill in the files `chainlink-secrets-api.yaml` and `chainlink-secrets-password.yaml`
3. Convert them to sealed secrets:

   ```bash
   kubeseal --format=yaml -f chainlink/manifests/chainlink-secrets-api.yaml > chainlink/manifests/chainlink-sealed-secret-api.yaml;
   kubeseal --format=yaml -f chainlink/manifests/chainlink-secrets-password.yaml > chainlink/manifests/chainlink-sealed-secret-password.yaml
   ```

4. Create them:

   ```bash
   kubectl create -f chainlink/manifests/chainlink-sealed-secret-password.yaml; 
   kubectl create -f chainlink/manifests/chainlink-sealed-secret-api.yaml
   ```

## Hardhat network

Runs a hardhat network to allow testing in local

1. Deploy the app: `kubectl apply -f hardhat-network/app-yaml`

## Deploy all the apps in ArgoCD

`kubectl apply --filename="./chainlink/app.yaml","./postgresql/app.yaml","./sealed-controller/app.yml"`
