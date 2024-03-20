# Kubernetes automated local deployment

## Goal

The goal of this simple project is to automate the deployment of a local Kubernetes cluster, using Vagrant (with Libvirt as a provider) and Ansible.

## Start the cluster

```bash
# Start k8s nodes with Vagrant
sudo vagrant up --provider=libvirt

# Configure the cluster with Ansible
ansible-playbook -i inventory.yml playbook.yml
```

