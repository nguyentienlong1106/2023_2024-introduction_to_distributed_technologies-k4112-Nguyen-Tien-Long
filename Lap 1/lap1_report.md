University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2024/2025
Group: K4112c  
Author: Nguyen Tien Long
Lab: Lab1  
Date of create: 10.12.2024  
Date of finished: 26.12.2023

#

## OBJECTIVE:

Get familiar with Minikube and Docker, deploy my first pod.

## COURSE OF WORK:

### 1. Be ready install docker on Ubuntu linux and running it.

### 2. After installing Minikube, deploy Minikube cluster with the command and install kubectl

```
minikube start
```

![image](./img/Screenshot%202024-11-07%20090656.png)

### 3. Create a deployment named vault, using image Vault, version 1.13.3 via use _yaml_ file (myfirst.yaml)

```
apiVersion: v1
kind: Pod
metadata:
  name: "vault"
  namespace: default
  labels:
    app: ""
spec:
  containers:
  - name: vault
    image: "vault:1.13.3"
    ports:
    - containerPort: 8200
```

This file specifies the pod's name and port:

- name: The name of the Pod, here it’s "vault".
- namespace: The Kubernetes namespace where the Pod is created (default is the default namespace).
- labels: Key-value pair for tagging this Pod. Here, app is a label but it’s currently empty.
- spec: Describes the desired configuration for the Pod.
- containers: Lists the containers to run inside this Pod.

After creating the _yaml_ file, run the following command to create a pod

This file creates a Pod named vault in the default namespace, with one container using the vault:1.13.3 image. The container exposes port 8200 for internal use.

```
kubectl apply -f myfirst.yaml
```

Port forwarding

```
kubectl port-forward pod/vault 8200:8200
```

### 4. Access the service using the link _localhost:8200_

![image](./img/Screenshot%202024-12-12%20125505.png)

### 5. In order to find the credentials, use this command

```
kubectl logs vault-6c599ff9f-ch2fm
```

The credentials are at the end of the output.

![image](./img/Screenshot%202024-12-12%20125932.png)

### 6. Use the credentials to authenticate, then we will be taken to this page

![image](./img/Screenshot%202024-12-12%20130138.png)

### 11. Stop the Minikube cluster

```
minikube stop
```

![image](./img/Screenshot%202024-12-12%20130303.png)

## DIAGRAM

![image](./img/Screenshot%202024-12-20%20231922.png)
