
This Repo having code for kubernetes installation, jenkins installation and jenkins pipeline setup for sample java application and deployed into Kubernetes cluster.

# Pre Requisites for the entire process
1 Terraform
2 Jenkins
3 Docker
4 Kubernetes Cluster

# terraform Installation
I Have followed below commands for Terraform installation on ubuntu local server.

```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=$(dpkg --print-architecture)] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update
sudo apt install terraform
```

# Infra Setup for Kubernetes installation

From the main.tf file having code for creation of VPC, Subnet and Security groups and creation of ec2 instances with ubuntu.
created 2 instacnes for 1. Control node 2 Worker node

executed commands
```
terraform init
terraform plan
terraform apply
```

Once infra setup is done, Now we have 2 ec2 instance.
# Installing CRI, kubeadm, kubelet and kubectl

SSH into instances

Copy the public IP of each of the two servers and replace them in the following command along with your key
```
chmod 400 k8s-key.pem
ssh -i KEY_NAME.pem ubuntu@PUBLIC_IP
```

Swap off is required by Kubernetes

```
sudo swapoff -a
```

Note: run sudo apt-get update in all two nodes before executing next commands


run below scripts for containerD installation
```
sh install_container.sh
service containerd status
```
```
sh install_kubeadm.sh
kubeadm version
service kubelet status
```
# Initialise Cluster

From the control plane node, please run
```
sudo kubeadm init
```
<img width="647" alt="image" src="https://github.com/user-attachments/assets/e237e5a1-9334-49b7-9938-bbe3bc21926a">

Once it finishes executing, copy the commands after “To start using your cluster, you need to run the following as a regular user:”

<img width="498" alt="image" src="https://github.com/user-attachments/assets/41cf6b87-4335-4fc4-8a0b-f469039a9fcc">

Check the status of the static pods

<img width="509" alt="image" src="https://github.com/user-attachments/assets/0e1859e5-306d-4aca-a140-64be5bff5d26">

# Deploy CNI Weave net

Weave Net is a popular Kubernetes add-on that provides networking and network policy solutions for containerized applications. It facilitates communication between containers across different hosts, creating a virtual network overlay

```
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```
<img width="463" alt="image" src="https://github.com/user-attachments/assets/9020b971-256a-4fb9-8e12-c05253a71be2">

Follow the same process in the other worker node except Kubeadm init command.

# Join worker nodes

When we executed sudo kubeadm init command, at the end of the verbose output, kubeadm gave a joining commands for the worker nodes.

```
kubeadm join 172.31.29*
```

Check worker nodes cluster joining status

![image](https://github.com/user-attachments/assets/0b93daff-5162-4319-be7b-55286fdd1c89)



# Jenkins Installation

Followed official documentation to install jenkins in aws ec2 node.

https://www.jenkins.io/doc/book/installing/linux/#debianubuntu

so configured Jenkins 

<img width="460" alt="image" src="https://github.com/user-attachments/assets/c79a3c0b-dfe7-4333-af12-dd5b750d0839">


Created Jenkins pipeline to deploy sample python app into kubernetes cluster.

installed Kubernetes CLI plugin for credentials configuration.

From python code, docker image has also been created as per pipeline step.

all pipeline steps are added in this github repo.

![image](https://github.com/user-attachments/assets/98fe3c3e-643c-4cf6-add7-070c50041fa6)

once deployed into Kuberntes cluster in appdeploy namespace,

<img width="389" alt="image" src="https://github.com/user-attachments/assets/4f3314d0-ce60-440c-af99-9cfd3149fd7f">

after deploying, i was able to access the heelo world application using cluster IP,

![image](https://github.com/user-attachments/assets/839f55a4-83d0-466e-bf1d-d96e19f84e2e)


# Prometheaus Installation

You can use the official Prometheus YAML templates from the Prometheus GitHub repository

```
git clone https://github.com/prometheus-operator/kube-prometheus.git
cd kube-prometheus/manifests
```
executing yaml files,

```
kubectl create -f setup/
kubectl create -f .
```
This deploys:

Prometheus
Alertmanager
Node Exporter
Grafana
Related Custom Resource Definitions (CRDs).

Verify the installation using below command,
```
kubectl get all -n monitoring
```
![image](https://github.com/user-attachments/assets/78f1af6b-01c7-4893-b83b-728d053a3f51)

