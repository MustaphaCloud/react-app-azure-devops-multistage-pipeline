# React App Azure DevOps Multi-Stage Pipeline

## Project Overview
This project deploys a React application to an Azure Ubuntu VM using a fully 
automated four-stage Azure DevOps pipeline triggered on every commit to the 
main branch. The pipeline builds, tests, publishes, and deploys the React app 
automatically with no manual intervention.

## Architecture
```
Azure Repos (my-react-app) --> Azure DevOps Pipeline --> Self-Hosted Agent --> Nginx on Azure VM
```

## Pipeline Stages
| Stage | What It Does |
|-------|-------------|
| Stage 1 - Build | Installs Node.js 18, runs npm install and npm run build |
| Stage 2 - Test | Runs automated unit tests, stops pipeline if tests fail |
| Stage 3 - Publish | Saves compiled /build folder as pipeline artifact |
| Stage 4 - Deploy | Copies artifact to /var/www/html on VM, restarts Nginx |

## Tech Stack
- **Infrastructure:** Terraform (Azure VM, VNet, NSG, Public IP)
- **Configuration:** Ansible (Nginx installation, SSH password auth)
- **CI/CD:** Azure DevOps Pipelines (four-stage YAML pipeline)
- **Runtime:** Node.js 18, npm
- **Web Server:** Nginx serving on port 80
- **Agent:** Self-hosted Azure DevOps agent on the VM

## Project Structure
```
react-app-pipeline/
├── terraform/
│   ├── main.tf          # Azure infrastructure resources
│   ├── variables.tf     # Input variables (update with your values)
│   └── outputs.tf       # Outputs the VM public IP
└── ansible/
    ├── playbook.yml     # Installs Nginx and enables SSH password auth
    └── inventory.ini    # Target VM host and connection variables
```

## Prerequisites
- Azure CLI installed and logged in
- Terraform installed
- Ansible installed
- Azure DevOps organisation created
- Node.js 18 available on the agent VM

## How to Reproduce This Project

### 1. Clone the repository
```bash
git clone https://github.com/MustaphaCloud/react-app-azure-devops-multistage-pipeline.git
cd react-app-azure-devops-multistage-pipeline
```

### 2. Provision the VM with Terraform
```bash
cd terraform
terraform init
terraform apply
```
Note the public IP from the output.

### 3. Configure the VM with Ansible
Update inventory.ini with your VM public IP and credentials, then run:
```bash
cd ../ansible
ansible-playbook -i inventory.ini playbook.yml
```

### 4. Azure DevOps Setup
- Import my-react-app repo into Azure Repos from:
  https://github.com/pravinmishraaws/my-react-app
- Create SSH Service Connection named ubuntu-nginx-ssh
- Create a self-hosted agent pool and install agent on the VM
- Create the four-stage YAML pipeline targeting the main branch

## Key Concepts
**Why React needs a build step:** React is written in JSX which browsers 
cannot read directly. Running npm run build compiles all JSX and JavaScript 
modules into optimised browser-readable files in the /build folder.

**Why artifacts matter:** The /build folder is published as an artifact in 
Stage 3 and downloaded in Stage 4. This guarantees the exact code that was 
tested is what gets deployed.

**Why --watchAll=false matters:** Without this flag, npm test runs in watch 
mode and the pipeline hangs indefinitely waiting for file changes.

## Author
Mustapha Nurudeen - DevOps Micro Internship (DMI) Cohort-2
GitHub: https://github.com/MustaphaCloud
