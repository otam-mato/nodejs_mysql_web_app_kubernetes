[under revision]

# nodejs_mysql_web_app_kubernetes



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

<img width="1000" alt="Screenshot 2023-10-03 at 23 17 54" src="https://github.com/otam-mato/nodejs_mysql_web_app_kubernetes/assets/113034133/23366684-f221-49ad-a793-aa345a014302">
