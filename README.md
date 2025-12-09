# n8n Deployments

## Introduction
This repository contains the configuration files to deploy an n8n instance using Docker Compose.
It's goal is to provide a simple and reproducible way to deploy n8n on different servers using GitHub Actions.

By default, the deployment uses Traefik as a reverse proxy and handles SSL termination using Let's Encrypt for a very simple setup.
If you deploy n8n to a server that has other uses, you might want to disable Traefik and use an external reverse proxy instead, see configuration details below.

## Server Prerequisites
- Docker and Docker Compose installed on the server.
- SSH access to the server.
- GitHub repository secrets configured for deployment.

## Quick Start
1. Fork or Clone this repository:
   ```bash
   git clone https://github.com/Norbz/n8n-deployments.git
   cd n8n-deployments
   ```

2. Set up your GitHub environment

3. Trigger the deployment in Github Actions.

## Generating and Adding an SSH Key

1. **Generate an SSH Key**:
   On your local machine, run the following command to generate an SSH key pair:
   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```
   - When prompted, specify a file to save the key (e.g., `~/.ssh/id_rsa`) or press Enter to use the default location.
   - Set a passphrase for added security (optional).

2. **Add the Public Key to Your Server**:
   Copy the public key to your server:
   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub user@your_server_ip
   ```
   Replace `user` with your server username and `your_server_ip` with your server's IP address or hostname.

3. **Test the Connection**:
   Verify that you can connect to the server without a password:
   ```bash
   ssh user@your_server_ip
   ```

4. **Add the Private Key to GitHub Secrets**:
   - Open your GitHub repository.
   - Click on **Settings** > **Environments**.
   - Create or select an environment (e.g., staging or production).
   - Add a new secret named `HOST_SSH_PRIVATE_KEY` to the environment settings and paste the contents of your private key (e.g., `~/.ssh/id_rsa`).

   **Important**: Never share your private key and keep it secure.

## Variables

As this repository aims at deploying community N8N to different servers, the variables will be grouped in an environment.
### Environments
**Create a github environment**:
- Go to your GitHub repository.
- Click on **Settings** > **Environments**.
- Create at least one environment

### Secrets
These variables should be added as your environment secrets:
- `HOST_SSH_PRIVATE_KEY`: SSH private key for server access.
- `HOST_USER`: Server username.
- `HOST_NAME`: Server hostname.
- `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`: Database credentials.

### Environment Variables
These variables should be defined as your environment variables:
- `HOST_REMOTE_PATH`: Path on the server where the project will be deployed.
- `N8N_SSL_EMAIL`: Email for SSL certificates.
- `N8N_SUBDOMAIN`, `N8N_DOMAIN_NAME`: Domain configuration.
- `ENABLE_TRAEFIK`: Set to `1` to enable Traefik, or `0` to disable it.

## Deployments
This repository uses GitHub Actions to deploy the application to a server.

1. **Ensure Your Environment is Set Up**:
   - Make sure you have created the necessary GitHub environment and added all required secrets and variables.
   - Make sure your server meets the prerequisites.
   - Make sure your SSH key is added to the server and GitHub secrets.
   - Make sure your user can write in `HOST_REMOTE_PATH`

2. **Run the Deployment Workflow**:
   - Go to the **Actions** tab in GitHub.
   - Select the **Deploy to Host** workflow.
   - Choose the environment (staging or production) and click **Run workflow**.

### What does the Github Action do?
The GitHub Action will simply connect to your server via SSH, copy the files in the specified remote path, create the .env file for you based on your github environment variables, and run `docker compose up -d --build` to start the n8n instance.

## Using an External Reverse Proxy
You might want to deploy n8n on a server that already has a Apache or set up to handle SSL termination and routing.
If you are using an external reverse proxy (e.g., Nginx or Apache), you can disable Traefik by setting `ENABLE_TRAEFIK=0` in your GitHub environment variables.
Refer to the [reverse proxy documentation](docs/reverse-proxies.md) for detailed setup instructions.

---

For more details, check the `docker-compose.yml` and `.github/workflows/deploy.yml` files.