University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2024/2025
Group: K4112c  
Author: Nguyen Tien Long
Lab: Lab 3  
Date of create: 10.12.2024  
Date of finished: 21.12.2023

#

## OBJECTIVE:

Get acquainted with certificates and “secrets” in Minikube, secure data storage in Minikube.

## COURSE OF WORK:

### 1. Create a config map with 2 variables REACT_APP_USERNAME and REACT_APP_COMPANY_NAME

```
kubectl create configmap myconfigmap --from-literal=REACT_APP_USERNAME=tienlong --from-literal=REACT_APP_COMPANY_NAME=ITMO -n reactapp
```

<img width="946" alt="image" src="./img/Screenshot 2024-12-18 181848.png">

### 2. Create a deployment with 2 replicas, using the config map to pass the environment variables to the pods

Create deployment manifest file

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  namespace: reactapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: reactapp
  template:
    metadata:
      labels:
        app: reactapp
    spec:
      containers:
      - name: reactapp
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
        - configMapRef:
            name: myconfigmap
```

Then apply the file to create the deployment

```
kubectl apply -f deployment.yaml
```

### 3. Expose our application using Service

```
kubectl expose deployment web-deployment --type=NodePort --port=3000 -n reactapp
```

<img width="851" alt="image" src="./img/Screenshot 2024-12-18 182655.png">

### 4. Install Ingress Controller in Minikube

```
minikube addons enable ingress
```

<img width="862" alt="image" src="./img/Screenshot 2024-12-18 183022.png">

### 5. Generate a self-signed certificate and import it to our Kubernetes cluster

Generate a self-signed certificate

```
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out MyCertificate.crt -keyout MyKey.key
```

<img width="960" alt="image" src="./img/Screenshot 2024-12-18 183119.png">

Import the certificate to our cluster by creating a secret named _my-certificate_

```
kubectl create secret tls my-certificate --key MyKey.key --cert MyCertificate.crt -n reactapp
```

<img width="938" alt="image" src="./img/Screenshot 2024-12-18 183151.png">

### 6. Create an ingress with the imported certificate

Create a manifest file for the ingress _ingress.yaml_

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-deployment
  namespace: reactapp
spec:
  tls:
  - hosts:
      - edu.info
    secretName: my-certificate
  rules:
  - host: edu.info
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-deployment
            port:
              number: 3000
```

Then apply it

```
kubectl apply -f ingress.yaml
```

<img width="490" alt="image" src="./img/Screenshot 2024-12-18 183308.png">

### 7. Edit hosts file to include our host name edu.info and launch Minikube tunnel

<img width="394" alt="image" src="./img/Screenshot 2024-12-18 184328.png">

```
minikube tunnel
```

### 8. Access the application via browser using the hostname edu.info

<img width="1258" alt="image" src="./img/Screenshot 2024-12-18 192544.png">

View the self-signed certificate

<img width="576" alt="image" src="./img/Screenshot 2024-12-18 192453.png">

## DIAGRAM

![image](./img/Screenshot%202024-12-21%20025622.png)
