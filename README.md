# Part 2: Gitops
Create a github repo with the following content:

### Service.yaml
```
apiVersion: v1
kind: Service
metadata:
 name: hello-node
spec:
 ports:
   - protocol: TCP
     port: 80
     targetPort: 3000
 selector:
   app: hello-node
```


### Ingress.yaml
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
 name: hello-node
spec:
 ingressClassName: nginx
 rules:
 - host: <the url u made up>.sctp-sandbox.com
   http:
     paths:
     - backend:
         service:
           name: hello-node
           port:
             number: 80
       path: /
       pathType: Prefix
```

### Deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
 name: hello-node
 labels:
   app: hello-node
spec:
 replicas: 1
 selector:
   matchLabels:
     app: hello-node
 template:
   metadata:
     labels:
       app: hello-node
   spec:
     containers:
     - name: node
       image: <your docker image>:latest
       ports:
       - containerPort: 3000
```
Add the application to argocd and sync the app


# Part 3 Creating your own helm chart (very hard)
```
Run helm create node-<your name>
```
Replace all content in templates folder with three files above. It should look like this:

<img width="158" alt="image" src="https://github.com/Reyarp85/node-zhiyong/assets/150350335/5ce90c03-a000-4219-af22-48943713621b">


## Run to get helm to login to ecr
```
aws ecr get-login-password \
    --region us-east-1 | helm registry login \
    --username AWS \
    --password-stdin 255945442255.dkr.ecr.us-east-1.amazonaws.com
```
## Then run
```
Helm package node-<your name>
```

Create your repository in aws with the same name
<img width="947" alt="image" src="https://github.com/Reyarp85/node-zhiyong/assets/150350335/058663a7-9464-4d2a-a315-764260882db4">


## Finally run to push the helm artifact to ecr.
```​​
helm push node-<your name>-0.1.0.tgz oci://255945442255.dkr.ecr.us-east-1.amazonaws.com/
```
