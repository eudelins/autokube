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

## Deploying apps on the cluster

First, you should upload your app images on a registry, such as `ghcr.io`.
Then you can use an authentication token in your cluster to access this registry during the app deployment:

```bash
sudo kubectl create secret docker-registry regcred --docker-server=ghcr.io --docker-username=$USER --docker-password=$TOKEN
```

Finally, transfer the kubernetes YAML files on the cluster and deploy your app:

```bash
# Transfer your kubernetes files and connect to the cluster
scp -i .ssh/id_rsa -r $KUBFILES_FOLDER vagrant@192.168.69.11:~
ssh -i .ssh/id_rsa vagrant@192.168.69.11

# Start the app
sudo kubectl apply -f $KUBFILES_FOLDER
```
