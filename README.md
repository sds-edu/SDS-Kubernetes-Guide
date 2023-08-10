# 1. A Guide to Getting Started with Kubernetes

<p align= "center">
<img src="images/kubslogo.png" width="250">
</p>

_**Table of Contents**_

## 1.1. Introduction
To a certain extent, Docker is widely used as a tool that makes it easy to package and deploy applications in containers. This ensures that applications behave consistently whether you are using them on your laptop or in the cloud.

However, as the demands of scalability and intricacy increase, the containerised Docker may not fully meet these requirements. This is where orchestration tools like Kubernetes come in. Many organisations are now adopting Kubernetes as an advanced abstraction layer to efficiently manage their containerised infrastructure. This simplifies workflows and enables teams to accelerate their processes.

<sup> The above text was partially generated with the help of [ChatGPT](https://chat.openai.com/)</sup>

<details open markdown="block">
<summary> <b> 🔍 Click here to find out more on why Kubernetes is widely used. </b></summary>

Kubernetes simplifies the deployment process for applications. It also provides tools that help make the application robust. The following table describes the key features and benefits of Kubernetes.

| Feature | Benefits |
| --- | --- |
| **Service discovery and load balancing** | Kubernetes can expose containers via DNS names or unique IP addresses, while also adeptly balancing and distributing high network traffic to ensure stable deployments. |
| **Storage orchestration** | Kubernetes provides the capability to seamlessly attach various storage systems of your preference, including local storage and public cloud providers, among others. |
| **Automated rollouts and rollbacks** | Kubernetes allows you to roll out new application versions without downtime, and also roll back to previous versions if required. |
| **Automatic bin packing** | You give Kubernetes a node cluster. Specify CPU and memory needs per container. Kubernetes optimizes container placement on nodes for efficient resource utilization. |
| **Self-healing** | Kubernetes automatically restarts failed containers, substitutes and reschedules containers in the event of node failures, terminates unresponsive containers based on user-defined health checks, and only exposes containers to clients once they are fully operational. |
| **Secret and configuration management** | Kubernetes enables secure storage and management of sensitive data like passwords, OAuth tokens, and SSH keys. You can deploy and update secrets and app configurations without rebuilding images or revealing secrets in your stack setup. |

<sup> This table was generated with the help of [Github Copilot](https://copilot.github.com/) and [ChatGPT](https://chat.openai.com/).</sup>

</details>

## 1.2. Installation and Setup
The most common ways to run a Kubernetes cluster locally are using [Minikube](https://minikube.sigs.k8s.io/docs/) **or** [Docker Desktop](https://www.docker.com/products/docker-desktop). This tutorial will focus on Minikube.

Minikube serves as a local Kubernetes solution, with a primary emphasis on simplifying Kubernetes learning and development. 

All you need is [Docker](https://www.docker.com/products/docker-desktop) (or similarly compatible) container **or** a [Virtual Machine](https://www.virtualbox.org/wiki/Downloads), and [Minikube](https://minikube.sigs.k8s.io/docs/) will take care of the rest.

You should have installed Docker for one of the previous tutorials. If you haven't, you can find the installation instructions [here](https://docs.docker.com/get-docker/).

Follow the instructions [here](https://minikube.sigs.k8s.io/docs/start/) to install Minikube for your OS. 

Once you are done, check that minikube is installed correctly by running:
```bash
minikube version
```
It should return the version of minikube and the commit hash.

Also check if you have `kubectl` installed by running:
```bash
kubectl version
```
You will need it to access your cluster. Alternatively, you can get minikube to install it for you by running:
```bash
minikube kubectl -- get po -A
```
If `kubectl version` returns both the client and server versions, you are good to go. 

## 1.3. Create a Kubernetes Cluster
This section will guide you through the process of deploying a local Kubernetes cluster using Minikube.

For this guide, we recommend the Docker driver for Minikube. If you would like to use a different driver (at your own risk), you can find the instructions [here](https://minikube.sigs.k8s.io/docs/drivers/).

> ⏰**Reminder**: Enable the Kubernetes service in Docker Desktop before proceeding. ![Enable Kubs](images/enablekubs.png)


Start the cluster by running:
```bash
minikube start --driver=docker
```
View cluster details using `kubectl cluster-info`. You'll get something similar to this:
```bash
Kubernetes control plane is running at https://127.0.0.1:64151
CoreDNS is running at https://127.0.0.1:64151/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

In this segment of the guide, we are focusing on the command line for deploying an application. However, you can also use the [Kubernetes dashboard](https://minikube.sigs.k8s.io/docs/handbook/dashboard/) to view your cluster. 

To see al nodes that can be used to host the applications, use the `kubectl get nodes` command. You should get something similar to this:
```bash
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   21m   v1.27.3
```
It can be seen that there is 1 node. The status is 'ready' which means it is ready to accept applications for deployment. 

## 1.4. Deploy an Application
This section will help you learn the basics of `kubectl` and deploy a sample application.

Deploy a sample application by running:
```bash
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```
This command will create a deployment called `kubernetes-bootcamp` and use the `gcr.io/google-samples/kubernetes-bootcamp:v1` container image. This image is taken from the Google Cloud Platform (GCP) registry.

This is what happens when create a deployment:
![deploykubs](images/deploykubs.png)

Run `kubectl get deployments` to see the deployment you just created:
```bash
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           51s
```
It can be seen that there is 1 deployment running a single instance of the application. It runs inside a Docker container in the node.

