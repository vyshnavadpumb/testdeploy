# Production Deployment Setup Guide

This guide provides step-by-step instructions for setting up the CI/CD pipeline to deploy your Docker Compose project to an Ubuntu server.

## 1. Server Requirements & Permissions
Ensure your Ubuntu server meets the following requirements:
- **Docker & Docker Compose**: Installed and running (`docker version`, `docker compose version`).
- **Git**: Installed (`git --version`).
- **User Permissions**:
    - The deployment user (e.g., `ubuntu`) should be in the `docker` group to run commands without `sudo`, OR use `sudo` with `NOPASSWD` configurations.
    - Deployment directory: `/var/www/myapp` must exist and be owned by the deployment user.
    ```bash
    sudo mkdir -p /var/www/myapp
    sudo chown $USER:$USER /var/www/myapp
    ```

## 2. SSH Key Generation
On your local machine or an environment where you can securely handle keys:
1. Generate a new SSH key pair:
   ```bash
   ssh-keygen -t ed25519 -C "github-actions-deploy" -f ./id_ed25519_deploy
   ```
   *Note: Using Ed25519 is recommended for security and performance.*

## 3. Adding SSH Key to Server
1. Copy the **Public Key** (`id_ed25519_deploy.pub`) to the server:
   ```bash
   ssh-copy-id -i id_ed25519_deploy.pub ubuntu@your-server-ip
   ```
2. Manually (Alternative): Append the content of `id_ed25519_deploy.pub` to `~/.ssh/authorized_keys` on the server.

## 4. GitHub Secrets Configuration
In your GitHub Repository, go to **Settings > Secrets and variables > Actions** and add the following:

| Name | Description | Example |
| :--- | :--- | :--- |
| `SERVER_HOST` | Server IP or Hostname | `203.0.113.42` |
| `SERVER_USER` | SSH Username | `ubuntu` |
| `SSH_PRIVATE_KEY` | Content of `id_ed25519_deploy` (Private Key) | `-----BEGIN OPENSSH PRIVATE KEY----- ...` |
| `SERVER_PORT` | SSH Port (optional, defaults to 22) | `22` |

## 5. Security Best Practices
- **Restrict SSH Access**: Use a specific user for deployment with minimal permissions.
- **Firewall**: Ensure port 22 (SSH) is only open to GitHub Actions IP ranges (using tools like `ufw` or cloud firewalls) if possible.
- **SSH Key Passphrase**: While GitHub Actions needs an unencrypted key, ensure the private key is stored securely in GitHub Secrets only.
- **Root Login**: Disable root SSH login in `/etc/ssh/sshd_config`.

## 6. Rollback Suggestion
If a deployment fails, you can manually roll back to the previous stable state on the server:
```bash
cd /var/www/myapp
git checkout [PREVIOUS_COMMIT_HASH]
docker compose down
docker compose up -d --build
```
*Tip: Consider using Docker tags for versioning instead of `latest` for easier rollbacks.*

## 7. Logging & Monitoring
- **GitHub Actions Logs**: View detailed logs for each deployment in the "Actions" tab.
- **Server Logs**: Check container logs on the server:
  ```bash
  docker compose logs -f --tail=100
  ```
