# aws-container-app
## Containerized Application Deployment using Docker and Azure Kubernetes Service (AKS) with auto-scaling and monitoring setup using Prometheus and Grafana.
# Containerization
I have taken the simple java code and convert into the image.
The commands are used to build an image.
1) For java application we have to take the maven. By using the below command it will create the automatic target file  in the form of .jar and copy the .jar file to the Docker file.
   ```
   mvn clean package
    ```
   ![image alt](https://github.com/pavithra-dev2003/aws-container-app/blob/main/Screenshot%202025-09-13%20235118.png?raw=true)
   
2) Build the image using the name called javaimage and also give the tag called 100.
   ```
   docker build -t iavaimage:100
   ```


   
3) After the build the image convert image into the container by using the run command using the detach mode and provide the container name as java and give port number and also mention the imagename.
   ```
   docker run -d --name java -p 8080:8080 javaimage:100
   ```
   
