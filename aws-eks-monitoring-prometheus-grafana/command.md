

 ## Create EKS cluster
  eksctl create cluster --name eks-cluster-209 --node-type t2.medium --nodes 2 --nodes-min 2 --nodes-max 3 --region eu-west-2

## Get EKS Cluster service
eksctl get cluster --name eks-cluster-209 --region eu-west-2
 
 ## Update eks kubeconfig once k8s cluster is installed successfully
aws eks update-kubeconfig --name eks-cluster-209

kubectl config current-context
kubectl cluster-info
kubectl get ns
kubectl get nodes
kubectl config svc
kubectl get events
kubectl get pods
kubectl get po
kubectl log <podName>
kubectl logs --since=6h <pod_name> #Print the logs for the last 6 hours for a pod
kubectl logs --tail=50 <pod_name>
kubectl logs -f <pod_name>
kubectl logs -c <container_name> <pod_name>
kubectl logs --previous <pod_name> #View the logs for a previously failed pod
kubectl apply -f service.yaml
kubectl get svc ngnix


# =============================

Install Kubernetes Metrics Server
# =================
Alright the next step would be to install the Kubernetes Metrics server onto the Kubernetes cluster so that Prometheus can collect the performance metrics of Kubernetes.

Kubernetes Metrics server collects the resource metrics from kubelets and exposes it to the Kubernetes api server through Metrics API for use by Horizontal Pod Autoscaler and Vertical Pod Autoscaler.

The Kubernetes metrics offers -

Fast autoscaling, collecting metrics
Scalable support up to 5,000 node clusters.
Resource efficiency and it requires very less cpu(1 mili core) and less memory(2 mb)
Installation of Kubernetes metrics server
Use the following installation script to install kubernetes metrics server

## install metric server

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml


## Verify the Kubernetes metric server installation- You can verify the 
# Kubernetes metric server installation 

kubectl get deployment metrics-server -n kube-system     or

kubectl get pods -n kube-system


## Install Prometheus
Now we have set up the Kubernetes cluster in the previous step, let's install the prometheus using the helm chart.

## Add Prometheus helm chart repository

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts 


"prometheus-community" has been added to your repositories

## Create Prometheus Namespace

kubectl create namespace prometheus

namespace/prometheus created

kubectl get namespace

## Install Prometheus using Helm

helm install stable prometheus-community/kube-prometheus-stack -n prometheus

## The above command is used to install kube-Prometheus-stack.

 The helm repo kube-stack-Prometheus comes with a Grafana deployment embedded ( as the default one ).

# To verify if Prometheus has been successfully installed using Helm on the EC2 instance, you

 can execute the following command:

kubectl get pods -n prometheus

NAME 
                                                    READY   STATUS    RESTARTS   AGE

alertmanager-stable-kube-prometheus-sta-alertmanager-0   2/2     Running   0          12m

prometheus-stable-kube-prometheus-sta-prometheus-0       2/2     Running   0          12m

stable-grafana-7566578c4f-q4v8p                          3/3     Running   0          12m

stable-kube-prometheus-sta-operator-8555f478ff-vkqrj     1/1     Running   0          12m

stable-kube-state-metrics-b65996c8d-kmttd                1/1     Running   0          12m

stable-prometheus-node-exporter-v2xrs                    1/1     Running   0          12m

stable-prometheus-node-exporter-z4t92                    1/1     Running   0          12m

# to check the services file (svc) of the Prometheus
kubectl get svc -n prometheus


The inclusion of Grafana alongside Prometheus in the stable version confirms the successful 

installation of Prometheus. Since Grafana is bundled with Prometheus, there’s no need for a 

separate installation.

Expose Prometheus and Grafana to the external word

Let’s expose Prometheus and Grafana to the external world

there are 2 ways to expose

1. through Node Port

2. through LoadBalancer

let’s go with the LoadBalancer

to attach the load balancer we need to change from ClusterIP to LoadBalancer

command to get the svc file


kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus

service/stable-kube-prometheus-sta-prometheus edited


## now confirm if this has been done
kubectl get svc -n prometheus

## As evidenced, a load balancer has been provisioned for Prometheus, allowing access via the

 link provided on port 9090. As shown below


a0e221809e10345029d9ae3e78a94070-1720925258.eu-west-2.elb.amazonaws.com:9090

## Now,let’s change the SVC file of the Grafana and expose it to the outer world

## command to edit the SVC file of grafana

kubectl edit svc stable-grafana -n prometheus

service/stable-grafana edited

#now get the svc again now it includes that of grafana

kubectl get svc -n prometheus

## Use the link found on the LoadBalancer for stable grafana to login

ad9b552b0d85f4db783fd3f5972c43ff-1568231178.eu-west-2.elb.amazonaws.com

# now you have the grafana interface, pls u need to now login use the command below to  get 

the secrest and password to login the username is admin


kubectl get secret --namespace prometheus stable-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

Now u are in grafana

# now the next question how dow get data into ntyhe datasource using prmetheus

## the Entire data of the cluster

where we can able to see the entire data of the EKS cluster

1. CPU and RAM use

2. pods in a specific namespace

. Pod up history

4. HPA
5. Resources by Container

CPU used by container & limits network bandwidth & packet rate


for dashboads checkout 
https://grafana.com/grafana/dashboards/




===========

## Get EKS Pod data.
kubectl get pods --all-namespaces

# Delete K8s Cluster
kubectl delete svc <serviceName>
kubectl delete -f <FileName>
eksctl delete cluster --name <ClusterName> --<regionName>

eksctl delete  cluster --name eks-cluster-209 --region eu-west-2