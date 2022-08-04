"# cert-manager" 
az aks get-credentials --resource-group [name] --name [container-name]

/alexandre# kubectl config current-context
hlg-containers-aks1

alexandre# kubectl get po -n kube-system
NAME                                  READY   STATUS    RESTARTS   AGE
azure-ip-masq-agent-d9jtc             1/1     Running   0          12d
cloud-node-manager-26fv2              1/1     Running   0          12d
coredns-87688dc49-2kvbq               1/1     Running   0          12d
coredns-87688dc49-ptfqq               1/1     Running   0          12d
coredns-autoscaler-6fb889cdfc-csf7v   1/1     Running   0          12d

helm install certi-manager --namespace kube-system bitnami/cert-manager

alexandre# helm install certi-manager --namespace kube-system bitnami/cert-manager
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /root/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /root/.kube/config
NAME: certi-manager
LAST DEPLOYED: Wed Jul 27 12:23:47 2022
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: cert-manager
CHART VERSION: 0.7.3
APP VERSION: 1.9.1

########### install cert-manager
C:\Users\alexandremonteiro\Documents\GitHub\cert-manager> kubectl apply -f .\cert-manager.yaml

########### config ClusterIssuer
C:\Users\alexandremonteiro\Documents\GitHub\cert-manager> kubectl apply -f .\hlog_issuer.yaml

########## Install Ingress

alexandre# kubectl create namespace ingress-basic

alexandre# helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

https://docs.microsoft.com/en-us/azure/aks/ingress-basic?tabs=azure-cli#basic-configuration
NAMESPACE=ingress-basic

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --create-namespace \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz
  
/alexandre# kubectl get pods -n ingress-basic
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-54d587fbc6-p4mzq   1/1     Running   0          3m20s

alexandre# kubectl get svc -n ingress-basic
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   0.0.0.xxx    0.0.0.xxx   80:31152/TCP,443:30947/TCP   4m44s
ingress-nginx-controller-admission   ClusterIP      0.0.0.xxx     <none>           443/TCP                      4m44s

https://docs.microsoft.com/en-us/azure/aks/ingress-tls?tabs=azure-powershell#method-1-set-the-dns-label-using-the-azure-cli
####### Config DNSNAME
  264  export IP="0.0.0.xxx "
  265  export DNSNAME="company-nginx"
  266  PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
  267  export PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
  268  echo PUBLICIPID
  269  echo $PUBLICIPID
  270  az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
  271  az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
  
alexandre# az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
company-nginx.eastus.cloudapp.azure.com

C:\Users\alexandremonteiro\Documents\GitHub\cert-manager> kubectl apply -f .\cluster-issuer.yaml

C:\Users\alexandremonteiro\Documents\GitHub\cert-manager> kubectl apply -f .\echo_ingress.yaml