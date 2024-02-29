# Ansible-lint Playbook Usage Guide

# Introduction
This playbook is used to perform ansible-lint on yaml files stored inside the repository in VTS Enterprise Github. This will check for best practices being followed in the playbook like FQCN (Fully Qualified Collection Name), missing Play name or Task name, names starting with upper case etc.

# Requirements
The playbook is intended to be executed via Automation Controller UI making use of the Execution Environment and since the primary use is to perform linting hence recommendation from ansible community is to use "creator-ee" container image which includes ansible-lint and other required packages.

The recommeded container image has to be exclusively added to Ansible Automation Platform as the installer doesn't include this image by default. Example snapshot for adding container image is as below:


For running playbook from command-line, following are to be installed as pre-requisites:
1. Ansible
2. Ansible-Navigator
3. Ansible-lint
4. Download "creator-ee" container image - podman pull quay.io/ansible/creator-ee

# Usage
## Automation Controller:
Ensure the Execution Environment is in place as per the requirements
### Configuration
- Project: 
  Add new project for ansible-lint providing name, organization,
- Template: 
  


