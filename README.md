# Mini Finance Pipeline - Azure DevOps CI/CD

## Project Overview
This project deploys the Mini Finance static website to an Azure Ubuntu VM 
using a fully automated Azure DevOps pipeline triggered on every commit to 
the main branch.

## Architecture
Azure Repos --> Azure DevOps Pipeline --> Self-Hosted Agent --> Nginx on Azure VM

## Tech Stack
- **Infrastructure:** Terraform (Azure VM, VNet, NSG, Public IP)
- **Configuration:** Ansible (Nginx installation, SSH password auth)
- **CI/CD:** Azure DevOps Pipelines (YAML pipeline)
- **Web Server:** Nginx serving on port 80
- **Agent:** Self-hosted Azure DevOps agent on the VM

## Project Structure
mini-finance-pipeline/
├── terraform/
│   ├── main.tf          # Azure infrastructure resources
│   ├── variables.tf     # Input variables (update with your values)
│   └── outputs.tf       # Outputs the VM public IP
└── ansible/
├── playbook.yml     # Installs Nginx and enables SSH password auth
└── inventory.ini.example  # Inventory template (rename and update)

## Prerequisites
- Azure CLI installed and logged in
- Terraform installed
- Ansible installed
- Azure DevOps organisation created

## How to Reproduce This Project

### 1. Clone the repository
```bash
git clone https://github.com/MustaphaCloud/mini-finance-pipeline.git
cd mini-finance-pipeline
```

### 2. Provision the VM with Terraform
```bash
cd terraform
terraform init
terraform plan
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
- Import mini_finance repo into Azure Repos
- Create SSH Service Connection named ubuntu-nginx-ssh
- Create a self-hosted agent pool and install agent on the VM
- Create the YAML pipeline using the provided azure-pipelines.yml

## Pipeline YAML
The pipeline YAML (azure-pipelines.yml) is stored in the Azure Repos 
repository. It triggers on commits to main, copies files to the VM via SSH, 
and deploys them to /var/www/html.

## Author
Mustapha Nurudeen - DevOps Micro Internship (DMI) Cohort-2
GitHub: https://github.com/MustaphaCloud