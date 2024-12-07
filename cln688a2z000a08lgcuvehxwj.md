---
title: "Learning Kubernetes and Kubectl"
datePublished: Sat Sep 30 2023 16:07:40 GMT+0000 (Coordinated Universal Time)
cuid: cln688a2z000a08lgcuvehxwj
slug: learning-kubernetes-and-kubectl
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/dMeEJRE18VI/upload/c674bfa9bbd60ebf1c3eb8efe21aa6de.jpeg
tags: kubernetes, devops

---

**Kubernetes**, often shortened to K8s, is a powerful tool for managing containerized applications. If you're new to Kubernetes, this blog post will introduce you to the basics of its architecture and provide you with some fundamental kubectl commands to get started.

## Kubernetes Architecture

Before diving into the commands, let's explore the fundamental components that make up a Kubernetes cluster:

1. **Master Node**: The control center of your Kubernetes cluster. It manages the overall state of the cluster, including scheduling, scaling, and monitoring. Key components of the master node include:
    
    * **API Server**: Acts as the front-end for the Kubernetes control plane, exposing the Kubernetes API.
        
    * **Scheduler**: Assigns work (containers) to nodes based on resource availability and constraints.
        
    * **Controller Manager**: Monitors the state of the cluster and takes corrective actions to maintain the desired state.
        
    * **etcd**: A distributed key-value store that stores the cluster's configuration data.
        
2. **Node**: Also known as a worker node or minion, it is where your application containers run. Key components of a node include:
    
    * **Kubelet**: Ensures that containers are running on the node as intended.
        
    * **Container Runtime**: The software responsible for running containers, such as Docker or containerd.
        
    * **Kube Proxy**: Maintains network rules on nodes and enables communication between pods.
        
3. **Pod**: The smallest deployable unit in Kubernetes. A pod can contain one or more containers that share the same network namespace. Containers within a pod can communicate with each other using localhost.
    
4. **Service**: A stable endpoint that abstracts the underlying pods. Services allow you to access your application without needing to know which specific pod is serving your request.
    
5. **Deployment**: Manages the desired state of your application, ensuring that a specified number of replicas are running and healthy at all times.
    

## Kubectl

**Kubectl** is a command-line tool used to interact with Kubernetes clusters. With kubectl, you can deploy, scale, inspect, and troubleshoot applications and services within your Kubernetes environment.

### Basic kubectl Commands

Now that we have a basic understanding of Kubernetes architecture, let's explore some essential kubectl commands to interact with your Kubernetes cluster:

1. **kubectl version**: Check the client and server version of kubectl and the Kubernetes cluster.
    
2. **kubectl cluster-info**: Display information about the Kubernetes cluster, including the API server address.
    
3. **kubectl get nodes**: List all the nodes in your cluster to see their status.
    
4. **kubectl get pods**: Retrieve a list of all pods running in the default namespace.
    
5. **kubectl create deployment &lt;name&gt; --image=&lt;image&gt;**: Deploy a containerized application to your cluster.
    
6. **kubectl get deployments**: List all deployments to see their current status.
    
7. **kubectl expose deployment &lt;name&gt; --port=&lt;port&gt; --type=&lt;service-type&gt;**: Expose a deployment as a service.
    
8. **kubectl scale deployment &lt;name&gt; --replicas=&lt;replica-count&gt;**: Scale the number of replicas for a deployment.
    
9. **kubectl logs &lt;pod-name&gt;**: View the logs of a specific pod.
    
10. **kubectl exec -it &lt;pod-name&gt; -- /bin/sh**: Access a shell inside a running pod for debugging and troubleshooting.
    

In conclusion, understanding Kubernetes and mastering kubectl are indispensable for modern software development and operations. It simplifies the management of containerized applications, streamlines deployment, and ensures the scalability and reliability of your services.