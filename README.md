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
Edit the **Vagrantfile** to set:
- Number of worker nodes
- CPU/RAM per node
- Static IPs
- Add your **public ssh key** in **node.vm.provision** section

3. create the VMs

```bash
vagrant up
```

4. Run Ansible Playbooks

Before running the script, do this
```bash
cd ~/auto-k8s-cluster/ansible
rm roles/kube_user/vars/sen.yml
ansible-vault encrypt vars/sen.yml
```
when you use ansible-vault it will prombt you for a password,
then add your public ssh key and password for the kube user. make sure to use **key** and **password** variable.

```yaml
key: "YOUR KEY"
password: "YOUR PASSWORD"
```

now you can run this
```bash
ansible-playbook -u vagrant --ask-vault-pass="your vault password" k8s_cluster.yml
```
the **k8s_cluster.yml** playbook will create kube user, prepare the nodes, init kubernetes on control node, and join the worker node to the cluster.

5. Verify the Cluster
ssh into the control node
```bash
ssh kube@control or ssh kube@"YOUR CONTROL VM IP"
kubectl get nodes
```
or use the vagrant user
```bash
ssh vagrant@"YOUR CONTROL NODE IP"
su - kube
kubectl get nodes
```
you should see all the nodes ready
