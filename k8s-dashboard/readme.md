# Using the Kubernetes Dashboard

**Step 1:** Go to the Katacoda Minikube Playground

`https://katacoda.com/javajon/courses/kubernetes-fundamentals/minikube`

and start minikube

`minikube start -p myminikube`

**Step 2:** Access the Kubernetes Dashboard that is part of the playground by clicking on the icon in the menu bar.

![Access K8S Dashboard 1](./images/create-dashboard-01.png)

**Step 3:** You'll be presented with the Katacoda web page to that will bind to the Kubernetes Dashboard. **Wait a minute or two for the bind mechanism to "warm up" and bind to the port.** The click the `Display Port` button on the web page.

![Access K8S Dashboard 2](./images/create-dashboard-02.png)

**Step 4:** Once presented with the Kubernetes, click on the `+ Create` button in the upper right of the UI.

![Access K8S Dashboard 3](./images/create-dashboard-03.png)

**Step 5:** Click the `CREATE FROM TEXT INPUT` then copy and paste the following content into the textarea that appears

```
apiVersion: v1 
kind: Pod 
metadata:
  labels:
    name: nginx-web
  name: nginx-web 
spec:
  containers:
    - image: nginx:1.11-alpine
      name: nginx-web 
      ports:
       - containerPort: 80 
         name: http 
         protocol: TCP
```

![Create Pod 1](./images/create-pod-01.png)

Then click the `UPLOAD` button.

**Step 6:** You'll see the that you used the Kubernetes Dashboard to create a pod.

![Create Pod 2](./images/create-pod-02.png)

**Step 7:** Let's create a Kubernetes Deployment and also a Service to access the pods in the deployment.

Click the `CREATE FROM TEXT INPUT` then copy and paste the following content into the textarea that appears:

```
apiVersion: v1
kind: Service
metadata:
  labels:
    name: hello-world-service
  name: hello-world-service
spec:
  selector:
    app: hello-world
  type: NodePort
  ports:
  - name: hello-world-deployment
    protocol: TCP
    port: 80
    nodePort: 30901
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: hello-world-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: lindison/hello-world:k8s
        ports:
        - containerPort: 80       
```
Then click the `UPLOAD` button.

![Create Deployment 1](./images/create-deployment-01.png)

You notice that now not only are there pods in the cluster, but there is also a deployment, `hello-world-deployment`. Also there are additional pods that are part of the `replicaset` that was created by the `deployment.

![Create Deployment 2](./images/create-deployment-02.png)

Also, because the manifest contained a declaration for a service, `hello-world-service`.

![Create Deployment 3](./images/create-deployment-03.png)

**Congratulations: Lab Complete!!!**



