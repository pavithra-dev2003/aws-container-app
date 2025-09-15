# Aws-Container-App 

## Containerized Application Deployment using Docker and Azure Kubernetes Service (AKS) with auto-scaling and monitoring setup using Prometheus and Grafana.

# 1) Containerization 
I have taken the simple java code and convert into the image. The commands are used to build an image. 
1) For java application we have to take the maven. By using the below command it will create the automatic target file in the form of .jar and copy the .jar file to the Docker file.
```
mvn clean package
```
![image alt]() 
2) Build the image using the name called javaimage and also give the tag called 100.
```
docker build -t javaimage:100
```
![image alt]) 

3) After the build the image convert image into the container by using the run command using the detach mode and provide the container name as java and give port number and also mention the imagename.
```
docker run -d --name java -p 8080:8080 javaimage:100
```
![image alt]
4) Login to the Docker Hub push the image to the docker hub
```
docker tag javaimage <username>/javaimage:100
docker login
docker push  <username>/javaimage:100
```
![img alt]
# 2) Deployment (Orchestration) 
1) Write the deployment file by taking the above image and apply this file by using the command kubectl
```
kubectl apply -f deployment.yaml
```
![img alt]
```
kubectl apply -f service
```
![img alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-14%20151414.png?raw=true) this is code i have write in the read me file this is correct or not if changes and give me back
