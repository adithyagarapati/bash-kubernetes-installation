# Kubernetes Installation Guide

This guide provides detailed instructions for setting up a Kubernetes cluster on machines running Ubuntu 24.04. It is designed to be generic, suitable for both cloud and on-premise environments.

## System Requirements

### Master Node
- **OS**: Ubuntu 24.04
- **CPU**: Minimum 2 cores
- **RAM**: Minimum 2 GB
- **Storage**: Minimum 10 GB available space

### Worker Nodes
- **OS**: Ubuntu 24.04
- **CPU**: Minimum 1 core
- **RAM**: Minimum 1 GB
- **Storage**: Minimum 10 GB available space

## Installation Steps

### STEP 1: Master Node Installation

**Objective**: Prepare the master node by installing necessary software and Kubernetes components.

1. **Set up a machine** that meets the above system requirements for the master node.
2. **Install Git** to clone the necessary Kubernetes installation scripts:

    ```bash
    sudo apt update && sudo apt install git -y
    git clone https://github.com/artisantek/kubernetes-installation
    cd kubernetes-installation
    bash install-kubernetes-1.30.sh
    ```

   This script installs Kubernetes and its dependencies. It automates the process of setting up Kubernetes, making it easier and faster.

### STEP 2: Prepare Worker Nodes

**Objective**: Ensure that all worker nodes are prepared using the same configuration as the master to maintain consistency.

1. **Set up machines** that meet the worker node system requirements.
2. **Use the same installation script** as the master node to ensure all nodes are configured identically:

    ```bash
    bash install-kubernetes-1.30.sh
    ```

### STEP 3: Initializing the Master Node

**Objective**: Initialize the Kubernetes cluster starting with the master node to manage the cluster.

1. **Log in to the master node**.
2. **Initialize Kubernetes** to start the cluster:

    ```bash
    sudo su
    kubeadm init
    ```

    > **Note:** Copy the command along with the token generated. This token is used to join worker nodes to the cluster. Keep it secure and accessible.

3. **Configure kubectl** to manage the Kubernetes cluster:

    ```bash
    exit
    mkdir -p $$HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $$HOME/.kube/config
    sudo chown $$(id -u):$$(id -g) $HOME/.kube/config
    ```

    This step configures your user to access the Kubernetes cluster management tools without needing additional permissions.

4. **Install a POD network** on the master node to enable communication between nodes:

    ```bash
    kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
    ```

    This network plugin allows pods to communicate with each other across node boundaries.

### STEP 4: Initialize Worker Nodes

**Objective**: Connect all worker nodes to the cluster using the token from the master node.

1. **SSH into each worker node**.
2. **Join the cluster** by using the token from the master node:

    ```bash
    sudo su
    kubeadm join <TOKEN>  # Use the command and token from STEP 3
    ```

### STEP 5: Verify the Cluster

**Objective**: Confirm that all nodes are connected and operational within the Kubernetes cluster.

1. **Log back into the master node**.
2. **Check the status of the nodes** to ensure they are all ready and communicating:

    ```bash
    kubectl get nodes
    ```

This setup ensures that you can deploy Kubernetes on any compatible hardware or cloud infrastructure that supports Ubuntu 24.04, following the same steps and requirements.