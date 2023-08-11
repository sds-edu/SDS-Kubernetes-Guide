# 1. A Guide to Getting Started with Kubernetes

<p align= "center">
<img src="images/kubslogo.png" width="250">
</p>

_**Table of Contents**_

## 1.1. Introduction
To a certain extent, Docker is widely used as a tool that makes it easy to package and deploy applications in containers. This ensures that applications behave consistently whether you are using them on your laptop or in the cloud.

However, as the demands of scalability and intricacy increase, the containerised Docker may not fully meet these requirements. This is where orchestration tools like Kubernetes come in. Many organisations are now adopting Kubernetes as an advanced abstraction layer to efficiently manage their containerised infrastructure. This simplifies workflows and enables teams to accelerate their processes.

<sup> The above text was partially generated with the help of [ChatGPT](https://chat.openai.com/)</sup>

<details markdown="block">
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

> ⏰**Reminder**: Start the Docker daemon and enable the Kubernetes service in Docker Desktop before proceeding. ![Enable Kubs](images/enablekubs.png)


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

> 📝**Note**: Kubernetes pods have a private network. They're visible within the cluster but not outside. When employing `kubectl`, we connect via an API endpoint to communicate with our application. 
The `kubectl` command is capable of generating a proxy, facilitating communication into the private network across the entire cluster. To halt the proxy, you can use control-C, and it will operate without displaying any output while active. 

<sup> This note was generated with the help of [ChatGPT](https://chat.openai.com/)</sup>

Open another terminal to run the proxy:
```bash
kubectl proxy
```
You will see something similar to this:
```bash
Starting to serve on 127.0.0.1:8001
```
Take note of the port number. In this case, it is 8001.

This runs the proxy in the background, return to your original terminal window. Now the host machine (your computer) can communicate with the Kubernetes cluster. You can access the APIs hosted through the proxy endpoint. Try querying the version through the API by running:
```bash
curl http://localhost:8001/version
```

> ⏰**Reminder**:Remember to replace the port number with the one you got from running `kubectl proxy`. Also note that the proxy must be running for this to work.

<details markdown="block">
<summary> <b>🔍 Click here to see the output.</b></summary>

```bash
StatusCode        : 200
StatusDescription : OK
Content           : {
                      "major": "1",
                      "minor": "27",
                      "gitVersion": "v1.27.3",
                      "gitCommit": "25b4e43193bcda6c7328a6d147b1fb73a33f1598",
                      "gitTreeState": "clean",
                      "buildDate": "2023-06-14T09:47:40Z",
                      "goVersion"...
RawContent        : HTTP/1.1 200 OK
                    Audit-Id: 5d958417-89e4-4835-aaf3-ae3f3530d535
                    X-Kubernetes-Pf-Flowschema-Uid: b4748383-acc3-468c-8172-90174f345c5d
                    X-Kubernetes-Pf-Prioritylevel-Uid: 0c632f7f-10da-49f9-8131-f80ef3...
Forms             : {}
Headers           : {[Audit-Id, 5d958417-89e4-4835-aaf3-ae3f3530d535], [X-Kubernetes-Pf-Flowschema-Uid,
                    b4748383-acc3-468c-8172-90174f345c5d], [X-Kubernetes-Pf-Prioritylevel-Uid,
                    0c632f7f-10da-49f9-8131-f80ef3043107], [Content-Length, 263]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 263
```
</details>

The API creates and endpoint for each pod in the cluster. To access the pod, you need to know its name. You can get the name of the pod by running:
```bash
kubectl get pods
```
You will get something similar to this:
```bash
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-855d5cc575-vjcmk   1/1     Running   0          44m
```
In this case, the pod name is `kubernetes-bootcamp-855d5cc575-vjcmk`. You can now access the pod through the API by running:
```bash
curl http://localhost:8001/api/v1/namespaces/default/pods/<PODNAME>
```
> ⏰**Reminder**: Remember to replace the port number with the one you got from running `kubectl proxy`. Also remember to replace `<PODNAME>` with the pod name you got from running `kubectl get pods`.

So your command will look something like this:
```
curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-bootcamp-855d5cc575-vjcmk
```

Note that this URL is the route to the API endpoint for the pod.

<details markdown="block">
<summary> <b>🔍 Click here to see the output.</b></summary>

```bash
StatusCode        : 200
StatusDescription : OK
Content           : {
                      "kind": "Pod",
                      "apiVersion": "v1",
                      "metadata": {
                        "name": "kubernetes-bootcamp-855d5cc575-vjcmk",
                        "generateName": "kubernetes-bootcamp-855d5cc575-",
                        "namespace": "default",
                        "uid...
RawContent        : HTTP/1.1 200 OK
                    Audit-Id: 092a5724-a854-4fc9-9b6b-b302345c63d9
                    X-Kubernetes-Pf-Flowschema-Uid: b4748383-acc3-468c-8172-90174f345c5d
                    X-Kubernetes-Pf-Prioritylevel-Uid: 0c632f7f-10da-49f9-8131-f80ef3...
Forms             : {}
Headers           : {[Audit-Id, 092a5724-a854-4fc9-9b6b-b302345c63d9], [X-Kubernetes-Pf-Flowschema-Uid,
                    b4748383-acc3-468c-8172-90174f345c5d], [X-Kubernetes-Pf-Prioritylevel-Uid,
                    0c632f7f-10da-49f9-8131-f80ef3043107], [Transfer-Encoding, chunked]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 7041
```

</details>

Yay! You just accessed the pod through the API. 

### 1.4.1 Explore the Application
View the containers running inside the pod by running:
```bash
kubectl describe pods
```
<details markdown="block">
<summary> <b>🔍 Click here to see the output.</b></summary>

```bash
Name:             kubernetes-bootcamp-855d5cc575-vjcmk
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.67.2
Start Time:       Fri, 11 Aug 2023 11:26:39 +0800
Labels:           app=kubernetes-bootcamp
                  pod-template-hash=855d5cc575
Annotations:      <none>
Status:           Running
IP:               10.244.0.3
IPs:
  IP:           10.244.0.3
Controlled By:  ReplicaSet/kubernetes-bootcamp-855d5cc575
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://3a955af528474f008155f8ec5c038a181988c0a4e7028c78d9a089b9f53023ca
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
    Image ID:       docker-pullable://gcr.io/google-samples/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 11 Aug 2023 11:26:54 +0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gbn5k (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-gbn5k:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  52m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-855d5cc575-vjcmk to minikube
  Normal  Pulling    52m   kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
  Normal  Pulled     52m   kubelet            Successfully pulled image "gcr.io/google-samples/kubernetes-bootcamp:v1" in 13.579247s (13.5792534s including waiting)
  Normal  Created    52m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    52m   kubelet            Started container kubernetes-bootcamp
```
</details>

Here we can see te details about the pods including the containers running inside the pod. Like IP address, image, container ID, etc.

### 1.4.2 View the Container Logs
You can view the logs for the pod by running:
```bash
kubectl logs <PODNAME>
```