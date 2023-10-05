[under revision]


# Node.JS + MySQL Web App.<br> Kubernetes "canary" deployment.

<br>

> **Note:** This is part of a series of demo projects in which I manipulate a Node.js application using various technologies.<br>
>
> The app built using Node.js and Express, originally presented at this [GitHub Repository](https://github.com/otam-mato/nodejs_mysql_web_app_terraform.git). The previous [project](https://github.com/otam-mato/nodejs_mysql_web_app_docker.git) involved deployment on Docker containers.
>
> In this current installment, we're taking a deeper dive by deploying the Dockerized version of the web application within a Kubernetes cluster. Subsequently, we'll introduce the second version of the app and employ the "canary" deployment strategy, routing approximately 33% of incoming traffic to this new version.
<br>

## Deployment Strategy

1. In this demo, we will deploy the app on a Kubernetes cluster using a "canary" approach:
   - V1 deployment will host the version 1 and be exposed to ~66% of traffic.
   - V2 deployment will host the version 2 and be exposed to ~33% of traffic.

   V1 and V2 versions uploaded into this [DockerHub repository](https://hub.docker.com/repository/docker/montcarotte/fullstack_nodejs_mysql_demo/general)

2. The app and the database will be placed in separate Kubernetes pods to secure decoupling, resource isolation, scaling and resilience.

<br>

## Technologies used
- AWS
- EKS
- Node.JS
- Express
- JavaScript
- MySQL
- Kubernetes
<br>

## Application Functionality

This web application interfaces with a MySQL database, facilitating CRUD (Create, Read, Update, Delete) operations on the database records.

**<details markdown=1><summary markdown="span">Detailed app description</summary>**

## Summary

The app sets up a web server for a supplier management system. It allows viewing, adding, updating, and deleting suppliers. 

#### **Dependencies and Modules**:
   - **express**: The framework that allows us to set up and run a web server.
   - **body-parser**: A tool that lets the server read and understand data sent in requests.
   - **cors**: Ensures the server can communicate with different web addresses or domains.
   - **mustache-express**: A template engine, letting the server display dynamic web pages using the Mustache format.
   - **serve-favicon**: Provides the small icon seen on browser tabs for the website.
   - **Custom Modules**: 
     - `supplier.controller`: Handles the logic for managing suppliers like fetching, adding, or updating their details.
     - `config.js`: Keeps the server's settings for connectind to the MySQL database.

#### **Configuration**:
   - The server starts on a port taken from a setting (like an environment variable) or uses `3000` as a default.

#### **Middleware**:
   - It's equipped to understand data in JSON format or when it's URL-encoded.
   - It can chat with web pages hosted elsewhere, thanks to CORS.
   - Mustache is the chosen format for web pages, with templates stored in a folder named `views`.
   - There's a public storage (`public`) for things like images or stylesheets, accessible by anyone visiting the site.
   - The site's tiny browser tab icon is fetched using `serve-favicon`.

#### **Routes (Webpage Endpoints)**:
   - **Home**: `GET /`: Serves the home page.
   - **Supplier Operations**: 
     - `GET /suppliers/`: Fetches and displays all suppliers.
     - `GET /supplier-add`: Serves a page to add a new supplier.
     - `POST /supplier-add`: Receives data to add a new supplier.
     - `GET /supplier-update/:id`: Serves a page to update details of a supplier using its ID.
     - `POST /supplier-update`: Receives updated data of a supplier.
     - `POST /supplier-remove/:id`: Removes a supplier using its ID.

#### **Starting Up**:
   - The server comes to life, starts listening for visits, and announces its awakening with a log message.

</details>

<br>

## Implementation Steps

Follow these steps for successful implementation:

1. [**Create infrastructure for your cluster**](https://github.com/otam-mato/nodejs_mysql_web_app_docker/blob/main/README.md#1-mysql-containerized-server)
2. [**Create a EKS cluster with 'eksctl'**](https://github.com/otam-mato/nodejs_mysql_web_app_docker/blob/main/README.md#2-nodejs-app-containerized-server)
3. [**Deploy the V1 app**](https://github.com/otam-mato/nodejs_mysql_web_app_docker/blob/main/README.md#3-test-the-app)
4. [**Deploy the V2 app**](https://github.com/otam-mato/nodejs_mysql_web_app_docker/blob/main/README.md#4-push-the-images-to-docker-hub)
5. [**Test the app**](https://github.com/otam-mato/nodejs_mysql_web_app_docker/blob/main/README.md#4-push-the-images-to-docker-hub)

<br>

## Screenshots

<p align="center">
  <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/7edead55-90b5-4e4e-9a45-eb9c2497547b" width="700px"/>
</p>

<p align="center">
  <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/a1a5042a-d944-4887-9a34-65e3a0959e3a" width="700px"/>

</p>


<p align="center">
  <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/b0f5ee8a-ed8f-4727-8cc0-e3f64779c401" width="700px"/>
</p>

<br>

## Architecture Diagram

<p align="center">
  <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/81d3e8e2-0623-4629-9096-8bc0bd28aee8" width="1000px"/>
</p>

<br>

## Prerequisites

Before starting, ensure the following prerequisites are met:

1. An AWS Cloud account.
2. Launch an EC2 instance (I am using Ubuntu 22.04) to be used as a master node.
   
3. <details markdown=1><summary markdown="span">Install and configure your AWS CLI.</summary>
   <br>
   `eksctl` relies on the AWS CLI for certain operations. If you don't have the AWS CLI installed, you can follow the AWS documentation to install it: [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
   
   </details>
4. <details markdown=1><summary markdown="span">Install `eksctl`.</summary>
   <br>
   Follow these steps to install `eksctl` on your Ubuntu system:

   - **Install `eksctl` using `curl`** (Recommended):
   
      You can use the following `curl` command to download and install `eksctl`:
   
      ```bash
      sudo curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | sudo tar xz -C /usr/local/bin
      ```
   
      This command will download the latest version of `eksctl` and extract it to the `/usr/local/bin` directory, making it accessible system-wide.
   
   - **Verify the installation**:
   
      After the installation is complete, you can verify that `eksctl` is installed by running the following command:
   
      ```bash
      eksctl version
      ```
   
      This command should display the version of `eksctl` if it was installed successfully.
   
   </details>
   
5. <details markdown=1><summary markdown="span">Install `kubectl`.</summary>
   <br>
   Follow this steps to install and configure the latest version of `kubectl` on your system.

   - **Download the latest version of `kubectl` using `curl`**:
      ```
      curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
      ```
   
   - **Make the downloaded `kubectl` binary executable**:
      ```
      chmod +x kubectl
      ```
   
   - **Move the `kubectl` binary to the `/usr/local/bin/` directory to make it available system-wide**:
      ```
      sudo mv kubectl /usr/local/bin/
      ```
   
   - **Restart your shell session to ensure that the updated `kubectl` version is recognized**:
      ```
      exec bash
      ```
   
   - **Finally, check the `kubectl` version to confirm the update**:
      ```
      kubectl version --client
      ```
   
   </details>

6. <details markdown=1><summary markdown="span">Create the infrastructure for your EKS cluster.</summary>
   <br>
   [The example of AWS CloudFormation template which you can use](https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/blob/305d93c75ea4fbfd5785f0862ac44e9a220f4642/EKS_sample_VPC.yml)

   </details>

7. <details markdown=1><summary markdown="span">Create your Kubernetes cluster in AWS EKS using `eksctl`</summary>
   <br>
   
   ```
   eksctl create cluster --name test-cluster4 --version 1.28 --region us-east-1 --nodegroup-name nodejs-nodes --node-type t2.micro --nodes 5
   ```
   <p align="center">
     <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/42ff2b42-1e9d-4b5a-87ff-f8264bc4034e" width="700px"/>
   </p>
   
   </details>

<br>

## Steps:

### 1. Create the deployment of V1 of the app
### 2. Create the service of a 'LoadBalancer' type to expose the cluster
### 3. Create the service for the MySQL pod to set up the communication with NodeJS containers
### 4. Create the deployment of V2 of the app
### 5. Scale up the replicas of V2 up to 2 pods (which equals approx. 33%)

```yml
kubectl scale deployment <deployment-name> --replicas=2
```
       
### 6. Scale down the replicas of V1 up to 4 pods (which equals approx. 66%)

```yml
kubectl scale deployment <deployment-name> --replicas=4
```

### 7. Test the app

- **Note the Load Balancer's DNS name**:

<br>

<p align="center">
   <img width="700" alt="Screenshot 2023-10-05 at 19 33 42" src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/0df05499-e5cd-41aa-840f-f5ba80f71ddd">
</p>

<br>

- **Paste the Load Balancer's DNS name into your browser and refresh the page a few times to see the traffic is distributed between the two versions of the app**:

<br>

<p align="center">
   <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/47360c3a-a568-456b-8dfc-67cc2e1bc948" width="300px"/>
   <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/47360c3a-a568-456b-8dfc-67cc2e1bc948" width="300px"/>
   <img src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/06d351e7-eba6-4c28-a44a-57a842d4070b" width="300px"/>
</p>
     
<br>

>**Note:** To achieve more advanced traffic distribution between different versions in a production environment, an Ingress controller like 'Istio' or 'NGINX ingress' is typically used. I plan to implement this advanced setup in my upcoming projects to keep the current one focused and straightforward.











---

Follow these steps to install `eksctl` on your Ubuntu system:

1. **Install `eksctl` using `curl`** (Recommended):

   You can use the following `curl` command to download and install `eksctl`:

   ```bash
   sudo curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | sudo tar xz -C /usr/local/bin
   ```

   This command will download the latest version of `eksctl` and extract it to the `/usr/local/bin` directory, making it accessible system-wide.

2. **Verify the installation**:

   After the installation is complete, you can verify that `eksctl` is installed by running the following command:

   ```bash
   eksctl version
   ```

   This command should display the version of `eksctl` if it was installed successfully.

3. **Add the AWS CLI** (if not already installed):

   `eksctl` relies on the AWS CLI for certain operations. If you don't have the AWS CLI installed, you can follow the AWS documentation to install it: [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)


Install and configure the latest version of `kubectl` on your system.

1. Downloaded the latest version of `kubectl` using `curl`:
   ```
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   ```

2. Made the downloaded `kubectl` binary executable:
   ```
   chmod +x kubectl
   ```

3. Moved the `kubectl` binary to the `/usr/local/bin/` directory to make it available system-wide:
   ```
   sudo mv kubectl /usr/local/bin/
   ```

4. Restarted your shell session to ensure that the updated `kubectl` version is recognized:
   ```
   exec bash
   ```

5. Finally, checked the `kubectl` version to confirm the update:
   ```
   kubectl version --client
   ```

 the latest version of `kubectl` installed and configured on your system.


 ```
eksctl create cluster \
--name test-cluster \
--version 1.28 \
--region us-east-1\
--nodegroup-name linux-nodes \
--node-type t2.micro \
--nodes 2 
```

```
echo -n "12345678" | base64

# output: MTIzNDU2Nzg=
```
<img width="1000" alt="Screenshot 2023-10-03 at 23 19 10" src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/cfadecee-4586-48e9-ba90-c82c8be08658">

