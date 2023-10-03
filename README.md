[under revision]

# nodejs_mysql_web_app_kubernetes

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
