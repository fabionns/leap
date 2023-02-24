# Instalar as controles 

### NGINX ingress controller
```shell
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.43.0/deploy/static/provider/baremetal/deploy.yaml
kubectl create ns ingress-nginx
kubectl apply -f deploy.yaml

# Cert-manager - https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
kubectl create namespace ldsec-cert-manager
kubectl config view
kubectl config current-context
kubectl config set-context dev --namespace=ldsec-cert-manager --cluster=kubernetes --user=kubernetes-admin
kubectl config set-context prd --namespace=ldsec-cert-manager --cluster=kubernetes --user=kubernetes-admin
kubectl config use-context dev
kubectl config current-context

kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
kubectl apply -f cluster-issuer.yaml

kubectl get pods -n ldsec-cert-manager
kubectl get certificates
kubectl get describe certificate letsencrypt-tls

# entrar no pod do cert-manager
```shell
kubectl exec -it cert-manager-5c7b7f7f9f-4j4xg -n ldsec-cert-manager -- /bin/sh
