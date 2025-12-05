# n8n Deployments

This repository contains the configuration files to deploy an n8n instance using Docker Compose. It supports multiple environments (staging and production).

## Prerequisites

- Docker and Docker Compose installed on the server.
- SSH access to the server.
- GitHub repository secrets configured for deployment.

## Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/Norbz/n8n-deployments.git
   cd n8n-deployments
   ```

2. Copy the `.env.example` file to `.env` and update the values:
   ```bash
   cp .env.example .env
   ```

3. Update the `docker-compose.yml` file if needed.

## Deployment

This repository uses GitHub Actions to deploy the application to a server.

1. Configure the following secrets in your GitHub repository:
   - `HOST_SSH_PRIVATE_KEY`: SSH private key for server access.
   - `HOST_USER`: Server username.
   - `HOST_NAME`: Server hostname.
   - `SSL_EMAIL`: Email for SSL certificates.
   - `SUBDOMAIN`, `DOMAIN_NAME`: Domain configuration.
   - `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`: Database credentials.

2. Trigger the deployment manually:
   - Go to the **Actions** tab in GitHub.
   - Select the **Deploy to VPS** workflow.
   - Choose the environment (staging or production) and click **Run workflow**.

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
   - Go to **Settings > Secrets and variables > Actions**.
   - Add a new secret named `HOST_SSH_PRIVATE_KEY` and paste the contents of your private key (e.g., `~/.ssh/id_rsa`).

   **Important**: Never share your private key and keep it secure.

## Updated Variables

1. **Set the Remote Path**:
   Define the `HOST_REMOTE_PATH` variable in your `.env` file to specify where the project should be deployed on the server. For example:
   ```bash
   HOST_REMOTE_PATH=/var/www/n8n-deployments
   ```

2. **N8N Variables**:
   Ensure the following variables are set in your `.env` file:
   ```bash
   N8N_SSL_EMAIL=example@example.com
   N8N_SUBDOMAIN=subdomain
   N8N_DOMAIN_NAME=example.com
   N8N_POSTGRES_USER=postgres_user
   N8N_POSTGRES_PASSWORD=postgres_password
   N8N_POSTGRES_DB=postgres_db
   ```

3. **Ensure Permissions**:
   Make sure the specified directory exists on the server and the user has the necessary permissions to write to it.

## File Structure

- `.env.example`: Example environment variables.
- `docker-compose.yml`: Docker Compose configuration.
- `.github/workflows/deploy.yml`: GitHub Action for deployment.

## Notes

- Ensure the `volumes` defined in `docker-compose.yml` exist on the server.
- The `traefik_data`, `n8n_data`, and `postgres_data` volumes must be created as external Docker volumes.

## Troubleshooting

- Check the GitHub Actions logs for deployment errors.
- Verify the `.env` file on the server is correctly populated.
- Ensure Docker and Docker Compose are running on the server.