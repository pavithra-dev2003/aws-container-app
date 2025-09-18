# Aws-Container-App 

## Containerized Application Deployment using Docker and Azure Kubernetes Service (AKS) with auto-scaling and monitoring setup using Prometheus and Grafana.

# 1) Containerization 
I have taken the simple java code and convert into the image. The commands are used to build an image. 
1) For java application we have to take the maven. By using the below command it will create the automatic target file in the form of .jar and copy the .jar file to the Docker file.
```
mvn clean package
```
![image alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-13%20235118.png?raw=true)  
2) Build the image using the name called javaimage and also give the tag called 100.
```
docker build -t javaimage:100
```
![image alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-14%20000751.png?raw=true)

3) After the build the image convert image into the container by using the run command using the detach mode and provide the container name as java and give port number and also mention the imagename.
```
docker run -d --name java -p 8080:8080 javaimage:100
```
![image alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-14%20000904.png?raw=true)
4) Login to the Docker Hub push the image to the docker hub
```
docker tag javaimage <username>/javaimage:100
docker login
docker push  <username>/javaimage:100
```
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-14%20005155.png?raw=true)
# 2) Deployment (Orchestration) 
1) Write the deployment file by taking the above image and apply this file by using the command kubectl
```
kubectl apply -f deployment.yaml
```
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-14%20115343.png?raw=true)
```
kubectl apply -f service
```
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-14%20151414.png?raw=true) 

# Containerized Application Deployment on AWS EKS
Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service provided by AWS. It makes it easy to run Kubernetes on AWS without needing to install and operate your own Kubernetes control plane.
# Configure AWS CLI with Access Key & Secret Key
Before deploying to EKS/ECR, you need to configure AWS CLI with your credentials.
* Run the AWS Configure Command
```
aws configure
```
#### Enter the Required Details
When prompted, provide the following:

* AWS Access Key ID → Your IAM user’s Access Key
* AWS Secret Access Key → Your IAM user’s Secret Key
* Default region name → Example: ap-south-1 (Mumbai), us-east-1 (N. Virginia)
* Default output format → json (recommended)
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20140036.png?raw=true)

## EKS Control Plane Creation
When we create an Amazon EKS cluster, AWS provisions and manages the control plane for us.
This includes:
* Kubernetes API Server → Handles requests from kubectl or other tools.
* etcd → Key-value store that saves cluster state and configuration.
* Scheduler → Decides which node runs each pod
* Controller Manager → Ensures the desired state of the cluster (like replicas, endpoints).
The control plane runs across multiple Availability Zones to ensure high availability. AWS automatically scales and updates it when needed.
```
eksctl create cluster --name my-eks-cluster --region ap-south-1 --version 1.29 --nodes 2 --node-type t3.medium --nodes-min 2 --nodes-max 5
```
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20140036.png?raw=true)
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20142516.png?raw=true)
#create oidc for eks
```
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster eksdemodeep --approve
```
eks cluster will be added as identity provider and it will let eks resources to access other aws services based upon sa created in cluster
command associates an IAM OIDC (OpenID Connect) provider with your Amazon EKS cluster
When is this needed?
If you plan to use IAM roles for service accounts (IRSA).
If your workloads need access to AWS services via IAM roles.
If you are deploying AWS Load Balancer Controller, EBS CSI driver, or other AWS-integrated add-ons.
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20141835.png?raw=true)
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20142040.png?raw=true)

## Creating a Node Group (Node Pool) in EKS

In Amazon EKS, Node Groups (also called Node Pools) define the set of worker nodes that run your application pods. You can add more node groups for different workloads (e.g., general apps, monitoring, GPU workloads.
```
eksctl create nodegroup --cluster my-eks-cluster --name my-nodegroup --region ap-south-1 --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 5 --managed
```
## Explanation of Flags
* --cluster → Name of the existing EKS cluster
* --name → Name of the node group
* --region → AWS region
* --node-type → EC2 instance type (e.g., t3.medium)
* --nodes → Initial number of nodes
* --nodes-min / --nodes-max → Auto-scaling limits
* --managed → Creates a Managed Node Group (AWS manages upgrades & health)

![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20153315.png?raw=true)
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-18%20153046.png?raw=true)
# Monitoring in EKS with Prometheus & Grafana

To ensure observability of the application and the EKS cluster, we use two open-source monitoring tools: Prometheus and Grafana.

# Prometheus
 Overview
* Prometheus is an open-source monitoring system that:
* Collects and stores time-series metrics (CPU, memory, pod usage, etc.).
* Supports PromQL (Prometheus Query Language) to filter and analyze data.

* Provides alerting through Alertmanager.

Ideal for Kubernetes monitoring because it integrates natively with kube-state-metrics and node-exporter.

## Installation Steps

Add Helm Repo
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Install Prometheus Stack
```
helm install prometheus prometheus-community/prometheus
```
edit svc file prometheus-server 
```
kubectl edit svc <serivcename>
```
Verify Installation
```
kubectl get pods -l "app=prometheus"
```
![img alt]()

##  Access Prometheus Dashboard

Forward Prometheus service to local system:
```
kubectl port-forward svc/prometheus-server 9090:80
```
* Open http://localhost:9090
Use PromQL queries to monitor metrics.
```
rate(node_cpu_seconds_total{mode="user"}[5m])
node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes
node_filesystem_size_bytes - node_filesystem_free_bytes
```
Example queries:
* CPU Usage: rate(container_cpu_usage_seconds_total[5m])
* Memory Usage: container_memory_usage_bytes
* Pod Restarts: kube_pod_container_status_restarts_total

# Grafana
### Overview
Grafana is an open-source visualization and analytics tool that:Connects to Prometheus as a data source.Displays metrics in interactive dashboards (graphs, charts, alerts).Supports user management, plugins, and integrations.

## Installation Steps

Install Grafana via Helm
```
helm install grafana grafana/grafana
```

Check Pods
```
kubectl get pods -l "app.kubernetes.io/name=grafana"
```

Access Grafana
```
kubectl port-forward svc/grafana 3000:80
```

Open http://localhost:3000
.

Default credentials:
* username: admin
* Password: admin (or check using kubectl get secret grafana -o jsonpath="{.data.admin-password}" | base64 --decode)

## Configure Grafana

Add Data Source → Prometheus (http://prometheus-server:80).
Import Dashboards from Grafana Labs Marketplace

* Kubernetes Cluster Monitoring (ID: 6417)
* Node Exporter Dashboard (ID: 1860)
* pod/Container Resource Usage

Dashboards

* Kubernetes Cluster Health → Node CPU, Memory, Disk.
* Pod Monitoring → Per-pod resource usage.
* Application Metrics → Custom metrics from /metrics endpoint.
* Alerting Rules → Email/Slack alerts when thresholds (e.g., CPU > 80%) are reached.=[



















