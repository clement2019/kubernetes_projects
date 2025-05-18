## Aws Eks Monitoring With Prometheus embeded with Grafana(not separated)

## What is Amazon EKS?
Amazon EKS (Elastic Kubernetes Service) is a managed Kubernetes service provided by Amazon Web Services (AWS). It simplifies the process of deploying, managing, and scaling containerized applications using Kubernetes on AWS infrastructure.
With Amazon EKS, users can leverage the power of Kubernetes without the complexity of managing the underlying infrastructure. EKS handles tasks such as cluster provisioning, upgrades, and scaling, allowing developers to focus on building and deploying applications.

## Example use-case Scenario
 Imagine a software development team wants to deploy a microservices-based application using containers. Instead of managing the Kubernetes cluster themselves, they can use Amazon EKS. They define their application’s architecture, specify resource requirements, and deploy it to the EKS cluster. Amazon EKS takes care of provisioning and managing the Kubernetes control plane, worker nodes, networking, and other infrastructure components. This allows the team to focus on developing their application while benefiting from the scalability, reliability, and flexibility of Kubernetes on AWS.


## What is Grafana?
Grafana is an open source tool for performing data analytics, retrieving metrics that make sense of large amounts of data, and monitoring our apps using nice configurable dashboards.
Grafana integrates with a wide range of data sources, including Graphite, Prometheus, Influx DB, ElasticSearch, MySQL, PostgreSQL, and others. When connected to supported data sources, it provides web-based charts, graphs, and alerts.

## What is Prometheus?
Prometheus is an open-source monitoring and alerting toolkit designed for reliability and scalability in modern, dynamic environments. Developed by the Cloud Native Computing Foundation, Prometheus excels at collecting and storing time-series data, allowing users to gain valuable insights into the performance and health of their applications and infrastructure.
With its powerful query language and support for multi-dimensional data, Prometheus has become a popular choice for monitoring systems within cloud-native ecosystems.
Monitoring AWS EKS Architecture

## Why Use Helm?

Helm serves as a package manager tailored for Kubernetes environments. With Helm, deploying complex applications or managing multiple resources becomes simplified and streamlined.
Here’s why leveraging Helm is advantageous:
Package Management: Helm offers a structured way to manage and organize Kubernetes manifests, making it easier to deploy and manage applications and services within your cluster.

Simplified Installation: Helm drastically simplifies the installation process by enabling you to install all required components with a single command. This saves time and reduces the chance of missing critical configuration steps.
Efficiency: By utilizing Helm, you can ensure that your Kubernetes deployments are efficient and consistent. Helm charts encapsulate best practices and standardized configurations, facilitating smoother deployments and reducing the risk of errors.
In essence, Helm empowers Kubernetes users to streamline their deployment workflows, enhance efficiency, and maintain consistency across their infrastructure.


for the complet list of kuberntes resources

kubectl api-resources

### Prerequisites

Before you start creating, you’ll need the following:

## an AWS account;
identity and access management (IAM) credentials and programmatic access;

AWS credentials that are set up locally with aws configure;

AWS Ubuntu 22.04 LTS Instance of type t2.small or t3.medium
.

Install some command-line tools .i.e. – eksctl, kubectl, and Helm Chart.

kubectl version --client

helm version

eksctl version

aws –version

Aws configure

## Kubernetes Cluster Creation

Now install the eks cluster using the command below:

eksctl create cluster --name=eks-cluster-amp-207 --region=eu-west-2 --version=1.30 --nodegroup-name=my-nodes-207 --node-type=t3.medium --managed --nodes=2 --nodes-min=2 --nodes-max=3

### Project proper

## Once the cluster is ready

#We can verify the cluster by logging into the AWS Console

## Confirm your cluster

eksctl get cluster --name eks-cluster-amp-207 --region eu-west-2

## Update Kube config by entering below command:

aws eks update-kubeconfig --name eks-cluster-amp-207 --region eu-west-2

Added new context arn:aws:eks:eu-west-2:759623136000000:cluster/eks-cluster-amp-207 to /home/ubuntu/.kube/config

## Connect to EKS cluster using kubectl commands

kubectl get nodes

kubectl get ns

#We need to add the Helm Stable Charts for your local client. Execute the below command:

helm repo add stable https://charts.helm.sh/stable

# Add Prometheus Helm Repository

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

## the Entire data of the cluster

where we can able to see the entire data of the EKS cluster

1. CPU and RAM use

2. pods in a specific namespace

. Pod up history

4. HPA
5. Resources by Container

CPU used by container & limits network bandwidth & packet rate

## Clean up/Deprovision-Deleting the Cluster

Now we will delete all our resources.

eksctl delete cluster --name eks-cluster-amp-207 --region=eu-west-2


## Conclusion:

In conclusion, setting up Prometheus and Grafana dashboards for monitoring AWS EKS offers a

 robust solution for observing and managing your Kubernetes clusters. With Prometheus 
 
 collecting metrics and Grafana providing visualization capabilities, users gain insights
 
  into cluster health, resource utilization, and performance metrics.






























































