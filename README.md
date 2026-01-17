K8S-Playbooks

A set of Ansible playbooks for automating the deployment of a Kubernetes cluster on four Debian 13 VMs. Designed for testing purposes, this project simplifies setting up a Kubernetes cluster with minimal manual intervention. Not production-ready.
Playbooks

This repository contains two Ansible playbooks that perform the following tasks:

    k8s-baseconfig.yaml: Prepares the Debian VMs for Kubernetes cluster setup using roles.
    k8s-kubeinstall.yaml: Initializes the Kubernetes cluster on the control node and joins the worker nodes using roles.

Prerequisites

    Four Debian 13 VMs, with access to SSH from your control machine.
    Ansible installed on the control machine.
    Ensure that your VMs have network connectivity with each other and the required ports are open.
    An inventory with control-plane and worker groups.

Configuration

    Update variables in group_vars/all.yml before running:

        k8s_control_plane_endpoint
        k8s_pod_cidr
        k8s_node_interface
        k8s_user_password

How to Use
Step 1: Configure Base Setup

Run the k8s-baseconfig.yaml playbook to prepare the VMs:

	ansible-playbook -i hosts k8s-baseconfig.yaml

This playbook will:

    Install necessary dependencies.
    Set up networking and firewall rules.
    Install CRI-O and Kubernetes components (kubelet, kubectl, and kubeadm).
    Load kernel module settings from crio.conf in this repo.

    Important:
    You must set k8s_user_password in group_vars/all.yml before running it.

Step 2: Set Up Kubernetes Cluster

Once the base setup is complete, run the k8s-kubeinstall.yaml playbook to initialize the cluster:

	ansible-playbook -i hosts k8s-kubeinstall.yaml

This playbook will:

    Initialize the control plane on the designated master node.
    Set up a Kubernetes configuration file for the kubernetes user.
    Install Calico as the networking solution.
    Generate a token to join the worker nodes to the cluster and apply it to the worker nodes.

    Important:
    Several variables in group_vars/all.yml need to be customized manually:

        k8s_control_plane_endpoint: IP address or DNS name of your control plane.
        k8s_pod_cidr: Desired Pod network CIDR range (e.g., 192.168.0.0/16).
        k8s_node_interface: Network interface name used to show IP info (e.g., eth0).

Inventory File

Ensure that you have a correctly formatted hosts file that lists all your VM IPs or hostnames. For example:

    [control_plane]
    10.0.0.10

    [workers]
    10.0.0.11
    10.0.0.12
    10.0.0.13

Post Setup

Once the playbooks have run successfully:

    Verify the control plane and worker nodes are correctly added by running kubectl get nodes on the master node.
    The cluster should be up and running, and ready for testing purposes.

Notes

    The playbooks are not production-ready and are intended for testing environments only.
    Manual modification of values in group_vars/all.yml is required before running the playbooks.
    Ensure that the control node's hostname and endpoint match across both playbooks.
