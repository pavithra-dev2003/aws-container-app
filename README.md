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
![img alt]()
























