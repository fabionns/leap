

# LAYER DATA OPS
## argocd
```shell
kubectl create namespace ldops-argocd
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd --namespace ldops-argocd --version 5.27.4
kubectl port-forward service/argocd-server -n ldops-argocd 8080:443
kubectl -n ldops-argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
kubectl describe pods -n ldops-argocd
kubectl patch svc argocd-server -n ldops-argocd -p '{"spec": {"type": "ClusterIP"}}'
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=system:serviceaccount:ldops-argocd:argocd-application-controller -n ldops-argocd

#### register cluster
CLUSTER="kubernetes-admin@kubernetes"
argocd cluster add $CLUSTER --in-cluster

# add repo into argo-cd repositories
REPOSITORY="https://github.com/fabionns/leap"
argocd repo add $REPOSITORY --username fabionns --password ckspa@bhzmg578 

argocd repo add git@github.com:fabionns/leap.git --ssh-private-key-path ~/.ssh/id_rsa
shell

### netie
```shell
kubectl create namespace ldops-nestie
```shell

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
kubectl apply -f 03-ldsto/minio/pv.yaml
kubectl apply -f 03-ldsto/minio/minio-operator.yaml
kubectl port-forward service/minio-operator -n ldsto-minio 9090:9090
kubectl get secrect $(kubectl get serviceaccount minio-sa --namespace ldsto-minio -o jsonpath='{.secrets[0].name}') --namespace ldsto-minio -o jsonpath='{.data.token}' | base64 --decode
eyJhbGciOiJSUzI1NiIsImtpZCI6InRoSDJ1VXZpUWpCZXhsMng3b3NyeDFCUFlGRFZxYl9OZUhtcW9tYU5XSkkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJtaW5pby1vcGVyYXRvciIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJjb25zb2xlLXNhLXNlY3JldCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJjb25zb2xlLXNhIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiODJiYmMwNzItZmJmMy00YmQ0LWI3MDctNWNiMDBmMmZlOGYxIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Om1pbmlvLW9wZXJhdG9yOmNvbnNvbGUtc2EifQ.EhymR6sAmBPUhPZyeukl00L8gG0IB-e82re6p8_Vg7i-SKFXgsUkUh0mMpgKCv1LChUPMYdW0wYlYSDfj32R5GdUvsQzfsPyR2eUIUbCCRXHG5Eoz2Hz64N6R1DaK72rUmKIg2gX46byr_2LWxwIDvxyNnu0xdhiU1Q8Uo-ONQNuNq83PpRUEXtHFxPYhCePiunGWa8y7jJEpOFycaaWNlbPn91eeVEHXryo-QIeJZzTXjRq0tZmJI5BOsf4EcaL1zaoVasDdquXnd-u80dVFpkIhmXV09_kRmjN2tKxKg2R7V1kaCI1zeQOL2Fyjq1Dbeig585vxjsRNNVclPiB1g

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


kubectl patch customresourcedefinitions.apiextensions.k8s.io applicationsets.argoproj.io -p '{"metadata":{"finalizers":null}}'
kubectl patch customresourcedefinitions.apiextensions.k8s.io applications.argoproj.io -p '{"metadata":{"finalizers":null}}'
kubectl patch customresourcedefinitions.apiextensions.k8s.io appprojects.argoproj.io -p '{"metadata":{"finalizers":null}}'

kubectl delete customresourcedefinitions.apiextensions.k8s.io applicationsets.argoproj.io
kubectl delete customresourcedefinitions.apiextensions.k8s.io applications.argoproj.io 
kubectl delete customresourcedefinitions.apiextensions.k8s.io appprojects.argoproj.io 



kubectl patch app minio-operator -p '{"metadata": {"finalizers": ["resources-finalizer.argocd.argoproj.io"]}}' --type merge 
kubectl delete Applications minio-operator

kubectl patch app minio-operator --type json --patch='[ { "op": "remove", "path": "/metadata/finalizers" } ]'
kubectl patch app minio-operator --type json -p '{ "op": "remove", "path": "/metadata/finalizers" }'