

# LAYER DATA OPS
## argocd
```shell
kubectl create namespace ldops-argocd
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd --namespace ldops-argocd --version 3.26.8
kubectl port-forward service/argocd-server -n ldops-argocd 8080:443
kubectl describe pods -n ldops-argocd
k patch svc argocd-server -n ldops-argocd -p '{"spec": {"type": "LoadBalancer"}}'
$ARGOCD="kubectl get services -l app.kubernetes.io/name=argocd-server,app.kubernetes.io/instance=argocd -o jsonpath="{.items[0].status.loadBalancer.ingress[0].ip}"
kubens ldops-argocd && kubectl get services -l app.kubernetes.io/name=argocd-server,app.kubernetes.io/instance=argocd -o jsonpath="{.items[0].status.loadBalancer.ingress[0].ip}""
kubens ldops-argocd && kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d | xargs -t -I {} argocd login $ARGOCD --username admin --password {} --insecure
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=system:serviceaccount:cicd:argocd-application-controller -n ldops-argocd
#### register cluster
CLUSTER="kubernetes-admin@kubernetes"
argocd cluster add $CLUSTER --in-cluster

# add repo into argo-cd repositories
REPOSITORY="https://github.com/fabionns/leap.git"
argocd repo add $REPOSITORY --username fabionns --password ckspa@bhzmg578 --port-forward
shell

### netie
kubectl create namespace ldops-nestie

### gitlab
kubectl create namespace ldops-gitlab

### lenses
kubectl create namespace ldops-lenses
kubectl apply -f 01-ldops/lenses/lenses.yaml

# -------------------------------------------------

# LAYER DATA SECURITY
### cert-manager
kubectl create namespace ldsec-certmanager
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.43.0/deploy/static/provider/baremetal/deploy.yaml
kubectl create ns ingress-nginx
kubectl apply -f deploy.yaml
### Cert-manager - https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml
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
### vault
kubectl create namespace ldsec-vault
kubectl create namespace ldsec-opa
kubectl create namespace ldsec-falco
kubectl create namespace ldsec-nginx
kubectl create namespace ldsec-keycloak
kubectl create namespace ldsec-istio

# layer data storage 
kubectl create namespace ldsto-minio
kubectl apply -f 03-ldsto/minio/minio-operator.yaml
kubectl get secrect $(kubectl get serviceaccount console-sa --namespace ldsto-minio -o jsonpath='{.secrets[0].name}') --namespace ldsto-minio -o jsonpath='{.data.token}' | base64 --decode


kubectl crate namespace ldsto-clickhouse
kubectl crate namespace ldsto-yugabyte
kubectl crate namespace ldsto-pinot
kubectl crate namespace ldsto-elasticsearch

# -------------------------------------------------

# layer data ingestion

# layer data governance

# layer data processing
### spark
kubectl create namespace ldproc-spark
helm install spark spark-operator/spark-operator --namespace ldproc-spark --set image.tag=v1beta2-1.3.3-3.1.1

# layer data exploration

# layer data smart

# layer data visualization

# layer data observability


