# k8s-admin-training

# Before start this training,you must have the below Pre-Requisites to loud better in the kubernetes Administration

- Linux Admiistration knowledge is must
- Ansible Automation/Administration Knowledge is must
- Google cloud account should be created and activated.
- Hub.docker.com login need to be created
- github.com account need to be created

** each VM should be like below **
  
  | Purpose   | VM Name          | CPU | Memory | Disk  | Operating System |
  | -------   | ---------------- | --- | ------ | ----  | ---------------- |
  | Machine 1 | k8s master       |  2  | 4 GB   | 50 GB | Ubuntu  18.0 LTS |
  | Machine 2 | Worker node1     |  2  | 4 GB   | 50 GB | Ubuntu  18.0 LTS |
  | Machine 3 | Worker node2     |  2  | 4 GB   | 50 GB | Ubuntu  18.0 LTS |



# 1st day session :

1. How Deployments were happening before kubernetes.
2. why we need kubernetes what is the actual need of that.
3. what is kubernets in a nutshell (definition).
4. Architecture of Kubernets.
5. Explanation of each componenets and its workflow.
6. what is pod.
7. installation of Kubernets.

https://github.com/cloudnloud/Kubernetes_Admin_Training/blob/main/class3-k8s-installation/installation.txt

# 2nd day session : 

# Creation of Namspace and pods

# create name space

```
kubectl create ns cloudnloud
```

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/namespace/namespace.yaml
```

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/namespace/namespace.yaml
```

# create pods...

```
kubectl create ns twitter
```
```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/1-singlepod.yml
```

```
kubectl apply -f 1-singlepod.yml
```
```
kubectl get all -n twitter -l app=nginx
```
```
kubectl get all -n twitter -l version=v1
```

```
kubectl exec -it pod/webserver bash -n twitter
```

- now you need to understand that lablels will help you to create exact report and find the particular resources

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/1-singlepod.yml
``` 
  
# Multipod.

```
kubectl create ns facebook

```

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/2-multipods.yml
```

```
kubectl get all -n facebook -l app=httpd
```

```
kubectl describe pod/multicontainer-pods -n facebook
```

```
kubectl exec -it pod/multicontainer-pods bash -n facebook
```

```
kubectl exec -it pod/multicontainer-pods bash -n facebook -c web
```

```
kubectl exec -it pod/multicontainer-pods bash -n facebook -c db
```
```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/2-multipods.yml
```

# Service: Nodeport. 

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class6-service/service/nodeport-facebook.yaml
```

```
kubectl describe pod/webserver -n facebook
```

```
kubectl get all -n facebook -o wide
```

 - now access master,node1,node2 public ip and check from outside browser

<Master Node Public IP>:32001
<Worker Node1 Public IP>:32001
<Worker Node2 Public IP>:32001

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class6-service/service/nodeport-facebook.yaml
```
# Day 3
  
# ReplicaSet - Single Replicaset

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/1-singlereplica.yml
```
```
kubectl get all -n twitter
```
```
kubectl describe replicas -n twitter
```
``` 
kubectl get pod,service,replicaset -n twitter -o wide
```
- labels --> service -- selector , replica -- match ---> should be same

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/1-singlereplica.yml
```
# ReplicaSet - Multi Replicaset

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/3-multireplica.yaml
```

```
kubectl get all -n facebook
```
```
kubectl describe replicas -n facebook
```
```
kubectl get pod,service,replicaset -n facebook -o wide
```
- labels --> service -- selector , replica -- match ---> should be same

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/3-multireplica.yaml
```
# Deployements  
```  
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class9-Deployments/deployments/1-deployment-facebook.yaml
```
```
kubectl get all -n facebook -o wide
```
```  
kubectl describe deploy
```
```  
kubectl get pod,service,replicaset,deploy -n facebook -o wide
```
- to understand please delete one pod (before delete in another master ssh window keep run the watch -n 1 kubectl get all -n facebook -o wide command)
```
kubectl delete pod/nginx-74fcc59689-kn5j2 -n facebook
```
then 
run the below command again
```  
kubectl get pod,service,replicaset,deploy -n facebook -o wide
```
change the new version of image in the existing 1-deployment-facebook.yaml --> 
image: nginx:1.17.6 (goto hub.docker.com and get the 1.17.6 version from nginx image)
from hub.docker.com you note the available image tags like below from nginx image.

1.20.1
1.21-perl
1.20-alpine
```
kubectl rollout status deployment/nginx -n facebook
```
```  
kubectl rollout history deploy/nginx -n facebook
```
```
kubectl rollout undo deployment nginx --to-revision=1 -n facebook
```
  
```
kubectl scale deployment/nginx --replicas=12
```
  
# Deamonset
  
- You have existing 3 node kubernetes setup (1 master node & 2 worker node)
- From master run this command and ensure all nodess are ready state
```
kubectl get nodes
```
```  
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class10-Daemonset/daemonset-nginx.yaml
```
```  
kubectl get all
```
- see now daemonset is 2 and running in each worker/client node.because daemonset is mandatory for each worker or client node.
- We have 1 master node 2 worker node now.So create one more worker node (3rd worker node).
- Now you need a kubernetes cluster join command with secret.so go to kube master node and run the below command.
```
kubeadm token create --print-join-command
```
(This command will help you to add more worker nodes whenever you want to add to KUBE cluster.Whenevr you want to add you need the kubeadm join command with new key and new hash key algorithm.Thats is where this command will help.However this command you must run only in KUBE master server.You shouldnt run from any worker nodes)
(you get some output from this command.

- Note: 3rd new worker need tohave all packages installed as we explained in class 3 k8s installation class.
- https://github.com/cloudnloud/Kubernetes_Admin_Training/tree/main/class3-k8s-installation

- before you join new worker node in master run the below command
```
watch -n 1 kubectl get all
```
go to master node and run the below command

- kubectl get nodes
- you  must see all nodes are ready state.
```  
kubectl get daemonsets
```
```  
kubectl describe daemonsets
```

# Day 4
  
# Init Containers
  
```
kubectl get nodes
```
- create new namespace called app1
```
kubectl create ns app1
```
- run the below command in one separate putty window in master server
```
watch -n 1 kubectl get all -n app1 -o wide
```  
```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class11-initcontainers/node-redis/node.yml -n app1
```
- first deploy this and run below command
- you will see init is waiting.coz the dependency is not yet launched or stopped
```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class11-initcontainers/node-redis/redis.yml -n app1
```
- if we deploy redis.yaml (kubectl apply -f redis.yaml then you run the kubectl get all command you will now see running state)
  
# Load Balancer

- For this load balancer exercise
- Goto Google cloud and create Google Kubernete Cluster Engine
- gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project model-axe-311317
```
kubectl create ns facebook
```
```  
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class6-service/service/loadbalancer-facebook.yaml
```
```  
kubectl get all -n facebook -o wide
```
```  
kubectl get pod,services -n facebook -o wide
```  
- 35.227.119.203:32001
- access like above from all node machine ip addresses.it should work from all client machine ip address.then the proxy cluster is working good.
```
kubectl get services
```
```  
kubectl describe services
```
  
# Ingress
  
# Introduction

Kubernetes Ingresses offer you a flexible way of routing traffic from beyond your cluster to internal Kubernetes Services. 
Ingress Resources are objects in Kubernetes that define rules for routing HTTP and HTTPS traffic to Services. 
For these to work, an Ingress Controller must be present; its role is to implement the rules by accepting traffic 
(most likely via a Load Balancer) and routing it to the appropriate Services. 

# Prerequisite
- Kubernetes Cluster up and running with master and node or GKE or EKS or any other type of k8s setup.
- Here we are using GCP as a cloud provider for LoadBalancer Service. In case if you do not want to use LoadBalancer you can skip the steps.


# Deploy hello-kubernetes-first.yaml
```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class14-ingress/hello-kubernetes-first.yaml
```
```  
kubectl get all -o wide
```
- To verify the Service’s creation, run the following command:
```
kubectl get service hello-kubernetes-first
```
- You’ll find that the newly created Service has a ClusterIP assigned, which means that it is working properly. All traffic sent to it will be forwarded to the selected Deployment on port 8080. Now that you have deployed the first variant of the hello-kubernetes app, you’ll work on the second one.


# Deploy hello-kubernetes-second.yaml
```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class14-ingress/hello-kubernetes-second.yaml
```
```  
kubectl get all -o wide
```
- To verify the Service’s creation, run the following command:
```
kubectl get service hello-kubernetes-second
```

# Installing the Kubernetes Nginx Ingress Controller
 
- To install the Nginx Ingress Controller to your cluster, you’ll first need to add its repository to Helm by running:
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```
- Update to let Helm know what it contains:
```
helm repo update
```
- Finally, run the following command to install the Nginx ingress:
```
helm install nginx-ingress ingress-nginx/ingress-nginx --set controller.publishService.enabled=true
```
- This command installs the Nginx Ingress Controller from the stable charts repository, names the Helm release nginx-ingress, and sets the publishService parameter to true.

- You can watch the Load Balancer become available by running:
```
kubectl --namespace default get services -o wide -w nginx-ingress-ingress-nginx-controller
```

# Exposing the App Using an Ingress
```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class14-ingress/hello-kubernetes-ingress.yaml
```
- You define an Ingress Resource with the name hello-kubernetes-ingress. Then, you specify two host rules, so that hw1.your_domain is routed to the hello-kubernetes-first Service, and hw2.your_domain is routed to the Service from the second deployment (hello-kubernetes-second).
```
kubectl get all -o wide
```
34.69.167.205	www.example.com
34.69.167.205	hw1.example.com
34.69.167.205	hw2.example.com

access in broswer now you can easily understand ingress
