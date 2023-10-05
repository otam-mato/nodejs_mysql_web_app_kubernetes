[under revision]

# Node.JS + MySQL Web App. Kubernetes "canary" deployment.

<br>

> **Note:** This is part of a series of demo projects in which I manipulate a Node.js application using various technologies.<br>
>
> The current installment involves deploying the web app in a Kubernetes cluster. The app built using Node.js and Express, originally presented at this [GitHub Repository](https://github.com/otam-mato/nodejs_mysql_web_app_terraform.git). The previous [deployment](https://github.com/otam-mato/nodejs_mysql_web_app_docker.git) was on Docker containers.
<br>

## Deployment Strategy

In this demo, we will deploy the app on a Kubernetes cluster using a "canary" approach:
1. V1 deployment will host the version 1 an be exposed to ~66% of traffick.
2. V2 deployment will host the version 2 an be exposed to ~33% of traffick.

Following a successful deployment of V2, the V1 can be deprecared.

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

For the Kubernetes deployment the app and the database will be placed in separate pods to secure decoupling, resource isolation, scaling and resilience.

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

