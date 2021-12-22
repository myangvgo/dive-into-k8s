# Introduction to Kubernetes

## 1. Create a Kubernetes Cluster

Create a Kubernetes Cluster with one master node and two worker nodes using `kubeadm`.

* [1. Install Ubuntu on VirtualBox](./01-k8s-cluster-setup/1-install-ubuntu-on-virtualbox.md)
* [2. Install Docker on Ubuntu](01-k8s-cluster-setup/2-install-docker-on-ubuntu.md)
* [3. Clone three VMs for Kubernetes Cluster](./01-k8s-cluster-setup/3-clone-three-vms-for-k8s-cluster.md)
* [4. Create Kubernetes Cluster with Kubeadm](./01-k8s-cluster-setup/4-create-k8s-cluster-kubeadm.md)
* [5. Install Metrics Server on Kubernetes](./01-k8s-cluster-setup/5-install-metrics-server.md)

IP of the master node: `192.168.56.10`
IP of the worker1 node: `192.168.56.11`
IP of the worker2 node: `192.168.56.12`

master, worker1, and worker2 are in the same network, they can ping each other.

## 2. Presentation

[introduction to kubernetes](./introduction-to-kubernetes.md)
