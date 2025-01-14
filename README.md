Scenario
---

A web app running on port 80 needs to be deployed to a Kubernetes install. This solution is to automate the deployment process pipeline, to containerize and deploy the application to Kubernetes. Ruby App runs on TCP port 80. The application also exposes a health check and endpoint at the route /healthcheck.

Solution
----

The solution comprises of below steps which will be detailed out in the next section. Following is the architecture diagram of the flow of steps :

![image](https://user-images.githubusercontent.com/68593337/120642718-ddc6e180-c492-11eb-896d-afd58db069c4.png)

1) Setup a Docker Image with a Docker File embedding the code of setting up the prerequisites for running the Ruby code which is injected to the images. Keep the Docker File on a directory with the Ruby Code and run below command within that directory (do not keep any other files part from those mentioned in the directory). Keep the Tag same as that in Docker Hub:

        docker build -t saket14/rubyhelloworld:1.0 .
    
![image](https://user-images.githubusercontent.com/68593337/120638631-fd0f4000-c48d-11eb-85fe-bd916769e7fb.png)

2) Push the Docker Image to Docker Hub Repository and store it there for the Kubernetes deployment to pick up. Push the image to the Docker Hub Repository using below command :

        docker push saket14/rubyhelloworld:1.0
    
![image](https://user-images.githubusercontent.com/68593337/120638697-144e2d80-c48e-11eb-8003-ed13ff93a58f.png)

3) Setup the container yaml file to deploy on Kubernetes pointing to the docker hub registry image - Define the Deployment with the Container picking up the image uploaded on Docker Hub and setup three POD Replicas which will be Load Balancer service. - To monitor the containers Deploy Liveness and Readiness to keep checking the health of the application and readiness to serve the traffic

4) Deploy the Application using the application yaml file on minikube Copy the Kubenetes Container Deployment file to your local drive from GitHub and run below command to deploy Kubernetes Application and Load Balancer Service:

       kubectl apply -f ruby-hello-world-container.yml
![image](https://user-images.githubusercontent.com/68593337/120640257-e10c9e00-c48f-11eb-8084-02ae353cf7ad.png)

5) Setup the service yaml file for load balancing the application on multiple Pods - Define the Deployment with the Service pointing to application deployed in Step 5. - Load Balancer as a service will balance the traffic on the pods

6) Deploy the Service using the application yaml file on minikube Copy the Kubenetes Service Deployment file to your local drive from GitHub and run below command to deploy Load Balancer Service :

        kubectl apply -f ruby-hello-world-service.yml
        
 ![image](https://user-images.githubusercontent.com/68593337/120641813-b885a380-c491-11eb-9105-8d3d59bf3b62.png)

7) The Application will be Load Balanced on Three Pod Replicas . Check it with below command: 

        kubectl get pods or kubectl get pod -o wide (for shorter screenshots haven’t used wide)

![image](https://user-images.githubusercontent.com/68593337/120642165-2af68380-c492-11eb-950c-8a9cbd2e43f6.png)

          kubectl get services (or -o wide)

![image](https://user-images.githubusercontent.com/68593337/120642208-3ba6f980-c492-11eb-8d67-f9f73ed439e3.png)

8) Check if the Webservice is running on the Browser :

![image](https://user-images.githubusercontent.com/68593337/120643035-44e49600-c493-11eb-9338-4080233fb849.png)

![image](https://user-images.githubusercontent.com/68593337/120643064-4f9f2b00-c493-11eb-803a-d83c908828fc.png)

9) Checking the probes for below purposes : 
        - “Liveness” Probes are checking if the URL is responding without any error if the URL fails it will restart the container 
        - “Readiness” Probes are checking if the URL with health qualifier is responding without any error if it fails it will not accept any incoming Traffic

    Below screenshot shows that the probes are doing their checks on the application containers:

              kubectl describe pod assignment-deployment

![image](https://user-images.githubusercontent.com/68593337/120650104-123e9b80-c49b-11eb-9a45-ee6672f0c31c.png)

10) Furthermore these Docker commands can be executed through a within a Task on Azure DevOps combining build/Push commands in one set and all kubectl files in another set
    And Deploy a Pipeline on Azure DevOps as below which will Build Docker Image , Push the Docker Image and Deploy to Kubernetes
    **As Azure DevOps will Charge you as per the Pipeline Runs and the usage of Docker/K8s tasks , I havent used it here**
    
    ![image](https://user-images.githubusercontent.com/68593337/120675780-02cb4c80-c4b3-11eb-936a-c459d1cb7f6b.png)


    ![image](https://user-images.githubusercontent.com/68593337/120675489-bed84780-c4b2-11eb-9efd-ef6ced394d3c.png)

    
