# testdeploy

This project provides a production-ready boilerplate for deploying a Docker Compose application to a remote Linux server using GitHub Actions.

## Project Structure
- `.github/workflows/deploy.yml`: The CI/CD pipeline.
- `docker-compose.yml`: Container orchestration (Nginx sample included).
- `html/`: Sample static website content.
- `setup-guide.md`: Detailed server and GitHub configuration instructions.

## Quick Start
1.  **Clone** this repository to your local machine.
2.  **Follow** the instructions in [setup-guide.md](./setup-guide.md) to:
    - Prepare your Ubuntu server.
    - Setup SSH keys.
    - Configure GitHub Secrets.
3.  **Push** changes to the `main` branch to trigger the deployment.

## Features
- **Automated Deployment**: Triggered only when `docker-compose.yml` changes.
- **Secure Access**: Uses SSH keys and GitHub Secrets.
- **Zero-Downtime Rebuilds**: Uses `docker compose up -d --build`.
- **Automatic Cleanup**: Prunes dangling images after deployment.

## License
MIT
