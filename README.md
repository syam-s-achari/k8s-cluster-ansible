# k8s-cluster-ansible
Deploy Kubernetes cluster on Ubuntu servers using ansible

This script will help you set up a Kubernetes cluster on bare-metal Ubuntu servers with kubeadm. I used this to set up a cluster with one master node and two worker nodes.

Make sure that:

The servers have at least 2GB RAM and 2 CPUs.
SSH access to the servers.
Disable swap on all machines.
Set up key-based access for all non-root users from the Ansible host (Laptop - i.e., the machine from which you are running Ansible playbooks).

1. Install Ansible on your Laptop:

apt install ansible

2. Update the IP addresses of the master and workers in the hosts file.

3. Create a non-root user on all machines and Kubernetes components.

ansible-playbook -i hosts initial.yml
ansible-playbook -i hosts kube-dependencies.yml  # This will install Docker, Kubeadm, Kubelet, kubectl, etc.

4. Next is to initialize the kubeadm and build the control plane. Then we will deploy the network (Flannel).

ansible-playbook -i hosts control-plane.yml

5. Login to the master and check the Kubernetes nodes:

kubectl get nodes
kubectl get pods -n kube-system

6. Add worker nodes to the cluster - This will generate the token to join nodes to the cluster.

ansible-playbook -i hosts workers.yml

Then check:

kubectl get nodes
kubectl get pods -n kube-system

If any of the nodes are not ready, wait for some time. If they still do not come online, manually generate the token and execute the join command on each node.
