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
1. Apply the manifests: `kubectl apply -f postgresql/manifests/ -n default` 
2. Test if it's running: `kubectl exec -it [pod-name] --  psql -h localhost -U admin --password -p 5432 postgresdb`