
# Bookwork Orchestration

This repository orchestrates the CI/CD pipeline for the Bookwork stack, including API, frontend, and infrastructure components. All build, push, deploy, and infrastructure provisioning steps are managed centrally from this repository.

## CI/CD Workflow Overview

The main workflow is defined in `.github/workflows/ci-cd.yaml` and is designed to:

- Build and push Docker images for the API and frontend to Google Artifact Registry
- Deploy the latest images to Google Cloud Run
- Provision and update GCP infrastructure using Terraform
- Allow selective deployment of API, frontend, and/or infrastructure via workflow parameters

### Workflow Trigger

The workflow is triggered manually using GitHub Actions' **workflow_dispatch** event. You can select which components to deploy:

- `deploy_api`: Build, push, and deploy the API service
- `deploy_frontend`: Build, push, and deploy the frontend service
- `deploy_infra`: Apply Terraform to update infrastructure

### Required Repository Secrets

The following secrets must be set in this repository:

- `GCP_PROJECT_ID`: Your GCP project ID
- `GCP_REGION`: The GCP region (e.g., `us-central1`)
- `GCP_SA_KEY`: Service account key JSON with permissions for Artifact Registry, Cloud Run, and Terraform
- `API_IMAGE`: Artifact Registry path for the API image (e.g., `us-central1-docker.pkg.dev/<project>/bookwork-api/api`)
- `FRONTEND_IMAGE`: Artifact Registry path for the frontend image (e.g., `us-central1-docker.pkg.dev/<project>/bookwork-frontend/frontend`)

### How the Workflow Works

1. **Setup**: Authenticates to GCP using the service account key.
2. **API Build & Deploy** (if enabled):
    - Checks out the latest API code
    - Builds and pushes a Docker image tagged with the commit SHA
    - Deploys the image to Cloud Run
3. **Frontend Build & Deploy** (if enabled):
    - Checks out the latest frontend code
    - Builds and pushes a Docker image tagged with the commit SHA
    - Deploys the image to Cloud Run
4. **Infrastructure Provisioning** (if enabled):
    - Checks out the infra repo
    - Runs `terraform init`, `terraform plan`, and `terraform apply`, passing the image tags as variables

### Usage Instructions

1. Commit and push changes to the API, frontend, or infra repositories as needed.
2. Go to the **Actions** tab in this repository on GitHub.
3. Select the **Bookwork Stack CI/CD** workflow.
4. Click **Run workflow** and set the desired parameters (`deploy_api`, `deploy_frontend`, `deploy_infra`).
5. Monitor the workflow run for build, push, deploy, and infra provisioning steps.

### Notes

- Images are always tagged with the commit SHA for traceability.
- Terraform is always passed the correct image tags to ensure Cloud Run services use the latest images.
- This repository is the single source of truth for Bookwork stack deployments.

---

For more details, see the `.github/workflows/ci-cd.yaml` file.
