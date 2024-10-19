# kubernetes-interview


Pre-requisites
- create a EKS cluster using demo-cluster.yaml
```
eksctl create cluster -f demo-cluster.yaml
```
## Helm : package manager 
* helm helps in installing the application amd handles all the dependency 

* Install helm
```
$ curl -LO https://git.io/get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh

```

## Monitoring kubenetes cluster 

ranjiniganeshan@Ranjinis-MacBook-Pro kubernetes-interview %  
```
kubectl create namespace prometheus
```
namespace/prometheus created

```
helm upgrade -i prometheus prometheus-community/prometheus \
  --namespace prometheus \
  --set alertmanager.persistentVolume.storageClass="gp2" \
  --set server.persistentVolume.storageClass="gp2"
```

Release "prometheus" does not exist. Installing it now.
NAME: prometheus
LAST DEPLOYED: Fri Oct 18 16:26:18 2024
NAMESPACE: prometheus
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
prometheus-server.prometheus.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace prometheus -l "app.kubernetes.io/name=prometheus,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace prometheus port-forward $POD_NAME 9090

eksctl utils associate-iam-oidc-provider --cluster cdit-cluster --approve

2024-10-18 16:28:14 [ℹ]  will create IAM Open ID Connect provider for cluster "demo-cluster" in "us-west-2"
2024-10-18 16:28:15 [✔]  created IAM Open ID Connect provider for cluster "demo-cluster" in "us-west-2"


The Prometheus alertmanager can be accessed via port 9093 on the following DNS name from within your cluster:
prometheus-alertmanager.prometheus.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace prometheus -l "app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace prometheus port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been disabled by default since    #####
######            it deprecated after k8s 1.25+. use                        #####
######            (index .Values "prometheus-node-exporter" "rbac"          #####
###### .          "pspEnabled") with (index .Values                         #####
######            "prometheus-node-exporter" "rbac" "pspAnnotations")       #####
######            in case you still need it.                                #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
prometheus-prometheus-pushgateway.prometheus.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace prometheus -l "app=prometheus-pushgateway,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace prometheus port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

export cluster_name="demo-cluster"

(venv) ranjiniganeshan@Ranjinis-MacBook-Pro kubernetes-interview % eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa\
  --namespace kube-system \
  --cluster $cluster_name \
  --role-name AmazonEKS_EBS_CSI_DriverRole \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve --role-only
```
2024-10-18 16:33:02 [ℹ]  1 iamserviceaccount (kube-system/ebs-csi-controller-sa) was included (based on the include/exclude rules)
2024-10-18 16:33:02 [!]  serviceaccounts in Kubernetes will not be created or modified, since the option --role-only is used
2024-10-18 16:33:02 [ℹ]  1 task: { create IAM role for serviceaccount "kube-system/ebs-csi-controller-sa" }
2024-10-18 16:33:02 [ℹ]  building iamserviceaccount stack "eksctl-demo-cluster-addon-iamserviceaccount-kube-system-ebs-csi-controller-sa"
2024-10-18 16:33:03 [ℹ]  deploying stack "eksctl-demo-cluster-addon-iamserviceaccount-kube-system-ebs-csi-controller-sa"
2024-10-18 16:33:03 [ℹ]  waiting for CloudFormation stack "eksctl-demo-cluster-addon-iamserviceaccount-kube-system-ebs-csi-controller-sa"
2024-10-18 16:33:35 [ℹ]  waiting for CloudFormation stack "eksctl-demo-cluster-addon-iamserviceaccount-kube-system-ebs-csi-controller-sa"

```
eksctl create addon --name aws-ebs-csi-driver --cluster $cluster_name --service-account-role-arn arn:aws:iam::909293070315:role/AmazonEKS_EBS_CSI_DriverRole --force

eksctl create addon --name aws-ebs-csi-driver --cluster $cluster_name --service-account-role-arn arn:aws:iam::909293070315:role/AmazonEKS_EBS_CSI_DriverRole --force
```
2024-10-18 16:41:51 [ℹ]  Kubernetes version "1.27" in use by cluster "demo-cluster"
2024-10-18 16:41:52 [ℹ]  using provided ServiceAccountRoleARN "arn:aws:iam::909293070315:role/AmazonEKS_EBS_CSI_DriverRole"
2024-10-18 16:41:52 [ℹ]  creating addon

```
kubectl get pvc,pods -n prometheus
```
NAME                                                      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/prometheus-server                   Bound    pvc-2d10600c-7a3e-4474-985f-882072c2b7fa   8Gi        RWO            gp2            81s
persistentvolumeclaim/storage-prometheus-alertmanager-0   Bound    pvc-7e44aa9a-1815-4d3f-9bb5-5a31f280c960   2Gi        RWO            gp2            6m49s

NAME                                                    READY   STATUS    RESTARTS   AGE
pod/prometheus-alertmanager-0                           1/1     Running   0          79s
pod/prometheus-kube-state-metrics-7f979f5c55-ch7vx      1/1     Running   0          79s
pod/prometheus-prometheus-node-exporter-2ghkm           1/1     Running   0          80s
pod/prometheus-prometheus-node-exporter-2x4cf           1/1     Running   0          80s
pod/prometheus-prometheus-node-exporter-4v2j9           1/1     Running   0          80s
pod/prometheus-prometheus-node-exporter-czcjv           1/1     Running   0          80s
pod/prometheus-prometheus-pushgateway-879d6dd6c-gz675   1/1     Running   0          79s
pod/prometheus-server-64589ccb46-66m2r                  2/2     Running   0          79s



```
kubectl label pod prometheus-server-64589ccb46-66m2r app=prometheus -n prometheus
```
pod/prometheus-server-64589ccb46-66m2r labeled
```
kubectl apply -f prometheus-service.yaml 
```
service/prometheus-nodeport created

* Access prometheus using any worker node IP external IP and nodeport

example 
kubectl get nodes -o wide 
NAME                                           STATUS   ROLES    AGE     VERSION                INTERNAL-IP      EXTERNAL-IP      OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-192-168-31-151.us-west-2.compute.internal   Ready    <none>   4h32m   v1.27.16-eks-a737599   192.168.31.151   54.186.87.219    Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-62-74.us-west-2.compute.internal    Ready    <none>   21m     v1.27.16-eks-a737599   192.168.62.74    54.245.54.245    Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-74-210.us-west-2.compute.internal   Ready    <none>   21m     v1.27.16-eks-a737599   192.168.74.210   34.219.14.97     Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-91-33.us-west-2.compute.internal    Ready    <none>   4h32m   v1.27.16-eks-a737599   192.168.91.33    54.203.106.116   Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22


* verify the prometheus service url 

kubectl run -i --tty --rm debug --image=busybox --restart=Never -- sh

If you don't see a command prompt, try pressing enter.
/ # wget -qO- http://prometheus-server.prometheus.svc.cluster.local
<!doctype html><html lang="en"><head><meta charset="utf-8"/><link rel="shortcut icon" href="./favicon.ico"/><meta name="viewport" content="width=device-width,initial-scale=1,shrink-to-fit=no"/><meta name="theme-color" content="#000000"/><script>const GLOBAL_CONSOLES_LINK="",GLOBAL_AGENT_MODE="false",GLOBAL_READY="true"</script><link rel="manifest" href="./manifest.json" crossorigin="use-credentials"/><title>Prometheus Time Series Collection and Processing Server</title><script defer="defer" src="./static/js/main.7a06df19.js"></script><link href="./static/css/main.0eb1f61f.css" rel="stylesheet"></head><body class="bootstrap"><noscript>You need to enable JavaScript to run this app.</noscript><div id="root"></div></body></html>/ # 


# grafana install

```
helm install grafana grafana/grafana \
--namespace grafana \
--set persistence.enabled=true \
--set persistence.storageClassName="gp2" \
--set adminPassword='EKS!sAWSome' \
--set service.type=NodePort
```
```
helm install grafana grafana/grafana \
>   --namespace grafana \
>   --set persistence.enabled=true \
>   --set persistence.storageClassName="gp2" \
>   --set adminPassword='EKS!sAWSome' \
>   --set service.type=NodePort

```

NAME: grafana
LAST DEPLOYED: Fri Oct 18 17:33:21 2024
NAMESPACE: grafana
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:

   kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

   grafana.grafana.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:
     export NODE_PORT=$(kubectl get --namespace grafana -o jsonpath="{.spec.ports[0].nodePort}" services grafana)
     export NODE_IP=$(kubectl get nodes --namespace grafana -o jsonpath="{.items[0].status.addresses[0].address}")
     echo http://$NODE_IP:$NODE_PORT

3. Login with the password from step 1 and the username: admin


TERNAL-IP     OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-192-168-0-84.us-west-2.compute.internal     Ready    <none>   52m   v1.27.16-eks-a737599   192.168.0.84     54.184.32.193   Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-16-150.us-west-2.compute.internal   Ready    <none>   52m   v1.27.16-eks-a737599   192.168.16.150   34.222.85.227   Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-20-211.us-west-2.compute.internal   Ready    <none>   52m   v1.27.16-eks-a737599   192.168.20.211   52.12.105.59    Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-44-132.us-west-2.compute.internal   Ready    <none>   52m   v1.27.16-eks-a737599   192.168.44.132   18.246.238.74   Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-53-144.us-west-2.compute.internal   Ready    <none>   52m   v1.27.16-eks-a737599   192.168.53.144   52.43.103.42    Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-81-172.us-west-2.compute.internal   Ready    <none>   52m   v1.27.16-eks-a737599   192.168.81.172   54.200.45.250   Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-82-7.us-west-2.compute.internal     Ready    <none>   52m   v1.27.16-eks-a737599   192.168.82.7     52.24.135.97    Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ip-192-168-85-152.us-west-2.compute.internal   Ready    <none>   52m   v1.27.16-eks-a737599   192.168.85.152   54.190.114.47   Amazon Linux 2   5.10.226-214.879.amzn2.x86_64   containerd://1.7.22
ranjiniganeshan@Ranjinis-MacBook-Pro kubernetes-interview % 


Access grafana from http://external-ip:nodeport
