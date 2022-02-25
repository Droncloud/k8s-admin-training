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
