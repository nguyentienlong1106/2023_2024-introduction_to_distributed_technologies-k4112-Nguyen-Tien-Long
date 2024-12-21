University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2024/2025
Group: K4112c  
Author: Nguyen Tien Long
Lab: Lab 4  
Date of create: 10.12.2024  
Date of finished: 26.12.2023

#

## OBJECTIVE:

Get acquainted with CNI Calico and the IPAM Plugin function, learn the features of CNI and CoreDNS.

## COURSE OF WORK:

### 1. Launch a 2-node cluster with CNI Calico installation

```
minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode
```

![image](./img/Screenshot%202024-12-19%20124557.png)

Get the list of nodes

```
kubectl get nodes
```

![image](./img/Screenshot%202024-12-19%20124736.png)

Verify Calico installation in the cluster

```
kubectl get pods -l k8s-app=calico-node -A
```

![image](./img/Screenshot%202024-12-19%20125113.png)

### 2. Label the nodes

![image](./img/Screenshot%202024-12-19%20125330.png)

### 3. Write a manifest file for assigning IP pool to the nodes based on their labels.

```
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: master-ippool
spec:
  cidr: 192.168.0.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: name == "master"
---
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: worker-ippool
spec:
  cidr: 192.168.1.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: name == "worker"
```

Apply the file

```
calicoctl apply -f ippool.yaml --allow-version-mismatch
```

We should now have two enabled IP pools, which we can see when running

```
calicoctl get ippool -o wide --allow-version-mismatch
```

![image](./img/Screenshot%202024-12-19%20175209.png)

### 4. Deploy an application and expose it using a service

Create a configmap

```
kubectl create configmap myconfigmap --from-literal=REACT_APP_USERNAME=tienlong--from-literal=REACT_APP_COMPANY_NAME=ITMO -n reactapp
```

Deploy the application using deployment.yaml file

```
kubectl apply -f deployment.yaml file
```

Expose the application

```
kubectl expose deployment web-deployment --type=NodePort --port=3000 -n reactapp
```

![image](./img/Screenshot%202024-12-19%20190309.png)

### 5. Enable port-forwarding

```
kubectl port-forward service/web-deployment -n reactapp
```

![image](./img/Screenshot%202024-12-19%20182540.png)

### 6. Access the application via browser

![image](./img/Screenshot%202024-12-19%20192421.png)

The container name and the container IP will not change even when we reload the page multiple times. To explain this, let's get the IP address of our pods

```
kubectl get pods -o wide -n reactapp
```

![image](./img/Screenshot%202024-12-19%20190522.png)

As we can see one pod is deployed in the master node with IP pool 192.168.0.0/24 and the other pod is deployed in the worker node with IP pool 192.168.1.0/24
The service only routes our request to the worker node, and that's why the container name and container IP fields do not change.

### 7. Connect to one of the pods and ping the other

Connect to the pod in the master node and ping the pod in the worker node

```
kubectl exec -it web-deployment-68fc94b898-s9vjz -n reactapp -- ping 192.168.1.129
```

![image](./img/Screenshot%202024-12-19%20190309.png)

The ping works.

## DIAGRAM

![image](./img/Screenshot%202024-12-21%20031112.png)
