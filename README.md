# Kubernetes Cluster Automation with KVM, Vagrant, and Ansible

## Overview
This project automates the provisioning of virtual machines and the setup of a Kubernetes cluster from scratch.
It uses  **KVM** as the virtualization backend, **Vagrant** for VM creation, and **Ansible** for configuring the cluster.

You can spin up a ready-to-use Kubernetes environment in minutes — ideal for local development, testing, or learning.

---

## Requirements

Make sure you have the following installed on your host machine (Fedora in my case):

Run the follwing:
```bash
sudo dnf install -y @virtualization libvirt vagrant vagrant-libvirt vagrant-sshfs vagrant-hostmanager ansible git ruby-devel
vagrant plugin install vagrant-libvirt
vagrant plugin install vagrant-hostmanager
pip3 install passlib
sudo systemctl enable --now libvirtd
export LIBVIRT_DEFAULT_URI=qemu:///system 
virsh --connect qemu:///system net-list --all
```

## Usage

1. Clone the repo

```bash
git clone https://github.com/khalidharbi/auto-k8s-cluster.git
cd auto-k8s-cluster
```

2. Configure VM
Edit the Vagrantfile to set:
- Number of worker nodes
- CPU/RAM per node
- Static IPs
- Add your **public ssh key** in **node.vm.provision** section

3. create the VMs

```bash
vagrant up
```

4. Run Ansible Playbooks

**Before running the first script use ansible-vault**
```bash
ansible-vault encrypt vars/sen.yml
when you use ansible-vault it will prombt you for a password
then add ur public ssh key and password for the kube user
```

```yaml
key: "YOUR KEY"
password: "YOUR PASSWORD"
```

now you can run this
```bash
ansible-playbook create_kube_user.yml

this will create kube for the k8s cluster

ansible-playbook prepare_nodes.yml
this will prepare the VMs for k8s 

ansible-playbook init_k8s.yml
this will start k8s on the control node

ansible-playbook join_cluster.yml
this will join the worker nodes to the k8s cluster

5. Verify the Cluster
ssh into the control node
```bash
ssh kube@control or ssh kube@"YOUR CONTROL VM IP"
kubectl get nodes
you should see all the nodes ready
