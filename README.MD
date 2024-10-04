K8S-Playbooks

A set of Ansible playbooks for automating the deployment of a Kubernetes cluster on four Ubuntu 24.04 VMs. Designed for testing purposes, this project simplifies setting up a Kubernetes cluster with minimal manual intervention. Not production-ready.
Playbooks

This repository contains two Ansible playbooks that perform the following tasks:

    k8s-baseconfig.yaml: Prepares the Ubuntu VMs for Kubernetes cluster setup.
    k8s-kubeinstall.yaml: Initializes the Kubernetes cluster on the control node and joins the worker nodes.

Prerequisites

    Four Ubuntu 24.04 VMs, with access to SSH from your control machine.
    Ansible installed on the control machine.
    Ensure that your VMs have network connectivity with each other and the required ports are open.

How to Use
Step 1: Configure Base Setup

Run the k8s-baseconfig.yaml playbook to prepare the VMs:

bash

ansible-playbook -i hosts k8s-baseconfig.yaml

This playbook will:

    Install necessary dependencies.
    Set up networking and firewall rules.
    Install CRI-O and Kubernetes components (kubelet, kubectl, and kubeadm).

    Important:
    You must manually replace the <<SECRET>> placeholder in the k8s-baseconfig.yaml playbook with a secure password for the kubernetes user before running it.

Step 2: Set Up Kubernetes Cluster

Once the base setup is complete, run the k8s-kubeinstall.yaml playbook to initialize the cluster:

bash

ansible-playbook -i hosts k8s-kubeinstall.yaml

This playbook will:

    Initialize the control plane on the designated master node.
    Set up a Kubernetes configuration file for the kubernetes user.
    Install Calico as the networking solution.
    Generate a token to join the worker nodes to the cluster and apply it to the worker nodes.

    Important:
    Several variables in the k8s-kubeinstall.yaml file need to be customized manually:

        ENDPOINT: Replace with the IP address or DNS name of your control plane (master node).
        HOSTNAME: Replace with the hostname of your control plane (master node).
        POD_CIDR: Replace with the desired Pod network CIDR range (e.g., 192.168.0.0/16).

    Additionally, replace CHANGE_THIS in several places with appropriate values:

        inventory_hostname == 'CHANGE_THIS' (control plane node hostname)
        The network interface name for your VM when gathering IP info (ip -4 addr show 'CHANGE_THIS').

Inventory File

Ensure that you have a correctly formatted hosts file that lists all your VM IPs or hostnames. For example:

Post Setup

Once the playbooks have run successfully:

    Verify the control plane and worker nodes are correctly added by running kubectl get nodes on the master node.
    The cluster should be up and running, and ready for testing purposes.

Notes

    The playbooks are not production-ready and are intended for testing environments only.
    Manual modification of the <<SECRET>> (in the base setup) and CHANGE_THIS placeholders (in the cluster setup) is required before running the playbooks.
    Ensure that the control node’s hostname and endpoint match across both playbooks.
