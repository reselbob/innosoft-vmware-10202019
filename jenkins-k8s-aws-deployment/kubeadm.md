# Creating a Kubernetes Cluster Using KubeAdm

**Note:** When creating your VMs makes sure that you open up port `6443` on the so that your Kubernetes command can access the API Server.

## Creating the Master Node

You're going to access the VM using `ssh certificate` athentication to access the various virtual machines.

**Step 1:** Set the rights on the certificate file.

`chmod 600 /path/to/your.pem`

**Step 2:** `ssh` into the virtual machine you created on AWS that you decided to use as the master node. 

`ssh -i /path/to/your.pem ubuntu@<aws_dns_for_master_node>`

**Step 3:** Run the command to become root user:

`sudo su -`

**Step 4:** Install `docker`

`apt update`

`apt install docker.io -y`

`systemctl enable docker`

**Step 5:** Install the packages required for Kubernetes on all servers as the root user. (You'll be asked to do this command when configuring worker nodes.)

`apt-get update && apt-get install -y apt-transport-https`

`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -`

**Step 6:** Create Kubernetes repository by running the following:

```echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list```

**Step 7:** Update your Linux environment and then install `kubelet`, `kubeadm` and `kubectl` on the Master Node

`apt-get update`

`apt-get install -y kubelet=1.16.0-00 kubeadm=1.16.0-00 kubectl`

**Step 8:** Run the following command on the initialize the master node

`kubeadm init --kubernetes-version=1.16.0 --ignore-preflight-errors=all`

If everything was successful you'll get the following:

`Your Kubernetes master has initialized successfully!`

**IMPORTANT!!!**: When you run `kubeadm init`, the command will generate a special  `kubeadm join` message which also conatains a unique `token` that you'll need when it comes time to add worker nodes to the cluster. Like so:

```
kubeadm join 172.31.83.141:6443 --token amr3r5.fxf1md3l31ft10ne \
    --discovery-token-ca-cert-hash sha256:34816ed74024657df8e1b11691ede751e164c5ebddb4beb07a1b1ac12d074d7c
```

**Save the entire command with the token.** You will need it later.

**Step 9:** Exit to ubuntu user:

`exit`

**Step 10:** Now configure server so you can interact with Kubernetes as the unprivileged user.

`mkdir -p $HOME/.kube`

`sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`

`sudo chown $(id -u):$(id -g) $HOME/.kube/config`

**Step 11:** Run following on the master to enable IP forwarding to IPTables.

`   sudo sysctl net.bridge.bridge-nf-call-iptables=1`

**Step 12:** Now we'll install a Pod overlay network on the master node:

` export kubever=$(kubectl version | base64 | tr -d '\n')`

``` curl -SL "https://cloud.weave.works/k8s/net?k8s-version=$kubever" | kubectl apply -f  -```

**Step 13:** We need to make sure the `coredns` is running. This can take a while. take a look at the pods in the namespace, `kube-system`. Eventually the `coredns` pods will get up and running.

`kubectl get pods -n kube-system`

You'll get a result similar to the following:

```
NAME                                       READY   STATUS    RESTARTS   AGE
coredns-5644d7b6d9-99bfj                   0/1     Running   0          6m16s
coredns-5644d7b6d9-dbp9p                   0/1     Running   0          6m16s
etcd-ip-172-31-83-141                      1/1     Running   0          5m9s
kube-apiserver-ip-172-31-83-141            1/1     Running   0          5m30s
kube-controller-manager-ip-172-31-83-141   1/1     Running   0          5m8s
kube-proxy-8bfn7                           1/1     Running   0          6m16s
kube-scheduler-ip-172-31-83-141            1/1     Running   0          5m10s
weave-net-sjq4k                            2/2     Running   0          34s
```

**Step 14:** Give `docker` permission to run as the default user, `ubuntu`:

`sudo usermod -a -G docker $USER`

## Creating the Worker Nodes
Now we need to add worker nodes to the cluster.

**Step 1:** `ssh` into the virtual machine you created on AWS that you decided to use as a worker node. 

`ssh -i /path/to/your.pem <your_id>@<aws_dns_for_worker_node>`

**Step 2:** Run the command to become root user:

`sudo su -`

**Step 3:** Install `docker`

`apt update`

`apt install docker.io`

`systemctl enable docker`

**Step 4:** Install the packages required for Kubernetes. 

`apt-get update && apt-get install -y apt-transport-https`

`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -`

**Step 5:** Create Kubernetes repository by running the following:

```echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list```

**Step 6:** Update your Linux environment and then install `kubelet`, `kubeadm` and `kubectl` on the Master Node

`apt-get update`

`apt-get install -y kubelet=1.16.0-00 kubeadm=1.16.0-00 kubectl`


**Step 7:** Run the `join` command that was emitted from the previous `kubeadm init` process above.

```sudo kubeadm join <command from kubeadm init output> --ignore-preflight-errors=all```

**Step 8:** Now we need to confirm that the workder node creation has succeeded. Log back into master and run:

`watch kubectl get nodes`.

If all is well you'll see the nodes and eventuall the nodes will enter a `Ready` state.

```
ubuntu@ip-172-31-83-141:~$ kubectl get nodes
NAME               STATUS   ROLES    AGE     VERSION
ip-172-31-82-150   Ready    <none>   2m55s   v1.16.0
ip-172-31-83-141   Ready    master   30m     v1.16.0
```

**Step 9:** Give `docker` permission to run as the default user, `ubuntu`:

`sudo usermod -a -G docker $USER`