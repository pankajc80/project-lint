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
- **Project:** 
  Add new project for ansible-lint providing name, organization, Source Control Type, Source Control URL, Source Control Branch as applicable, Default Execution Environment to be "creator-ee" contaner image
- **Template:**
  Add a new Job Template selecting the Project created above and select "main.yml" playbook to be executed when this job runs.
  For the Inventory field, select "localhost" or "Demo Inventory" which points to localhost only as this playbook is not intended to run on any managed host but to run in offline mode at container level for linting the playbook files.
- **Survey:**
  The Playbook requires certain user inputs that will be used inside the playbook to perform the desired tasks. These user inputs will be provided via Survey feature within the Job Template. Open the newly created Job Template, go to        Survey tab and add new Survey with below details:
    -  **Provide GitHub Token** (mandatory) - Required to perform various GIT operations.
    -  **GitHub Team Name** (mandatory) - Repository from the specified team will be considered
    -  **Repository Name** (optional) - If provided, only mentioned repositories will be worked upon else all repositories for the given team will be considered
    -  **Source Branch** (mandatory) (applicable values: prod or linted) - To specify the branch from where playbook files will be picked to perform linting and applicable fixes.
        - prod branch: Original branch which contains the playbook files and need to be linted.
        - linted branch: This is the new branch that will be created by this playbook from prod branch and after linting has been performed, the updated files along with the reports will be committed in this branch.

### Execution Methodology
As part of the playbook run, in addition to this new branch i.e. "linted", the playbook will create another branch named "prod_linted" which will contain updated playbook files as applicable excluding the reports so that when user merge the updated files to "prod" branch, they will create a pull request from "prod_linted" to "prod" so that reports are excluded from the merge.

After playbook execution is completed on "prod" branch for desired team and repository, user will review the lint reports for any recommendations and errors, some fixes might already be applied during this playbook run however there 
might be some cases where user need to perform desired action based on the lint report. 

User need to update the files in "linted" branch, commit them and then Launch the lint job template however this time with Source Branch as 
"linted". The playbook will again perform linting on the updated files and create new reports and create a pull request from "prod_linted" to "prod".

User need to review and if no recommendations or errors reported then they can approve the merge.

## Command Line Execution
Please ensure to have all the pre-requisites covered as mentioned under Requirements section for running the ansible-lint playbook from the command line.
1. Create a directory where you want to maintain local repository
2. Clone the repository and cd to the repo directory
3. Update the var file to include GitHub Token, GitHub Team Name, Repository Name and Source Branch
4. Execute the command: ansible-navigator run main.yml --eei creator-ee:latest --pp missing -m stdout -vv
  


