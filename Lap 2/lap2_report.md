University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2024/2025
Group: K4112c  
Author: Nguyen Tien Long
Lab: Lab 2  
Date of create: 10.12.2024  
Date of finished: 21.12.2023

#

## OBJECTIVE:

Get acquainted with different types of container deployment, get acquainted with network services and deploy a web application.

## COURSE OF WORK:

### 1. Create a deployment with 2 replicas based on the image _ifilyaninitmo/itdt-contained-frontend_.

First of all, create a yaml manifest file

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  labels:
    app: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web-container
        image: ifilyaninitmo/itdt-contained-frontend:master
        env:
        - name: REACT_APP_USERNAME
          value: tienlong
        - name: REACT_APP_COMPANY_NAME
          value: ITMO
```

This file passes environment variables to our 2 replicas.
After that run this command to create the deployment.

```
kubectl apply -f mysecond.yaml
```

![image](./img/Screenshot%202024-12-12%20193221.png)

### 2. Create a service to expose our deployment to external traffic

```
kubectl expose deployment/web-deployment --type="NodePort" --port 3000
```

![image](./img/Screenshot%202024-12-12%20193234.png)

### 3. Launch port-forwarding mode to access our containers through browser

```
kubectl port-forward service/web-deployment 3000:3000
```

![image](./img/Screenshot%202024-12-12%20193242.png)

### 4. Access the web page through a browser

![image](./img/Screenshot%202024-12-12%20193159.png)

The 2 environment variables REACT_APP_USERNAME and REACT_APP_COMPANY_NAME will always be the same. However the container name can change, depending on which pod the service routes the traffic to.

## DIAGRAM

![image](./img/Screenshot%202024-12-20%20234427.png)
