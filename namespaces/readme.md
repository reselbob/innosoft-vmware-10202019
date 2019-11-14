# Working with Kubernetes Namespaces

The purpose of this lab is to provide an introduction to using [Kuberentes Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

In this lab you're going to create a pod dedicated to particular namespace then learn how to inspect the pod according to it's namespace.

**Step 1:** Go to the Katacoda Kubernetes Playground

`https://katacoda.com/courses/kubernetes/playground`

**Step 2:** Using a the `vi` editor that is built into the Playground, create a file, `namespaced-pod.yaml` with the following contents:

```yaml
---
kind: Namespace
apiVersion: v1
metadata:
  name: training
  labels:
    name: training
---
apiVersion: v1
kind: Pod
metadata:
  namespace: training
  name: pinger
  labels:
    app: pinger
spec:
  containers:
  - name: pinger
    image: reselbob/pinger:v2.4
    ports:
      - containerPort: 3000
    env:
      - name: CURRENT_EXERCISE
        value: COOL_TRAINING
```

**Step 3:** Apply the manifest file to create the namespace and pod in Kubernetes

`kubectl apply -f namespaced-pod.yaml`

You'll get the following output:

`pod/pinger created`

**Step 4:** Try to find the pod, `pinger`:

`kubectl get pods`

You'll get following output:

`No resources found.`

Why? Because executing `kubectl get pods` implies looking only in the `default` namepace.

**Step 5:** Expand the scope of finding the pod:

`kubectl get pods --all-namespaces`

You'll get output similar to the following:

```text
NAMESPACE     NAME                                       READY   STATUS             RESTARTS   AGE
kube-system   coredns-fb8b8dccf-gm5ql                    1/1     Running            0          60m
kube-system   coredns-fb8b8dccf-mq5lh                    1/1     Running            0          60m
kube-system   etcd-master                                1/1     Running            0          59m
kube-system   kube-apiserver-master                      1/1     Running            0          58m
kube-system   kube-controller-manager-master             1/1     Running            0          58m
kube-system   kube-keepalived-vip-2plxz                  1/1     Running            0          59m
kube-system   kube-proxy-ksvsq                           1/1     Running            0          60m
kube-system   kube-proxy-q9nzz                           1/1     Running            0          60m
kube-system   kube-scheduler-master                      1/1     Running            0          59m
kube-system   weave-net-mv45x                            2/2     Running            1          60m
kube-system   weave-net-rds8d                            2/2     Running            1          60m
training      pinger                                     1/1     Running            0          3m24
```
Now you are seeing all pods in all namespaces.

**Step 5:** Narrow the scope of finding the pod to the namespace `training`:

`master $ kubectl get pods -n training`

You'll get output that shows only pods in the namespace, `training`.

```
NAME     READY   STATUS    RESTARTS   AGE
pinger   1/1     Running   0          6m8s
```

**Congratulation! Lab Complete!**


