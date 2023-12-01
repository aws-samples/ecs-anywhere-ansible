## Bootstrapping ECS Anywhere nodes with Ansible

This project provides sample Ansible playbooks for registering and deregistering nodes to/from an Amazon ECS Cluster.

### Solution Overview

The solution is comprised of two Ansible playbooks and handles common prerequisites for the most common use cases and patterns. 

The register.yml playbook will run OS updates, installs the AWS CLI, jq, and curl packages (used later by the playbook), and use the provided IAM credentials to register and join each server to AWS Systems Manager (SSM) and your ECS Cluster. In order to automatically and securely establish trust between the on-premises server and the ECS control plane, the ECS agent makes use of the SSM Agent that is deployed on the on-premises server. Using a hardware fingerprint, the SSM Agent rotates IAM credentials every 30 minutes. The SSM Agent automatically updates the credentials when the external instance reconnects to AWS if it loses connectivity. For registration, the playbook first queries to ensure the host has not already been registered. If it has it will skip the remaining steps. If not, it will continue with registration. 

The deregister.yml playbook will check to ensure the host is registered before continuing. If it is, it will locate the instance ID from the local IP address of the instance, then find the Container Instance Arn in ECS from the instance ID. It will then drain the instance being removed to ensure all container have been rescheduled on other instances before removing the instance from both SSM and ECS. The drain stage will loop for up to 10 minutes waiting for the node to be fully drained. If, after 10 minutes, the node is not drained the playbook will set the host back to ACTIVE and skip the remaining steps. 

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.