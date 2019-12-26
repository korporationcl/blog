---
title: "Kubernetes"
date: 2019-12-26T19:35:38+01:00
draft: false 
tags: []
---
# Cluster Architecture

Kubernetes is composed of a master node (Control Plane) and workers (where containers are running). Some key aspects from each role are:

## Control Plane (master node):

- API Server: communication hub for all the components, exposes Kubernetes API to all the other components.
- Scheduler: is in charge of assigning applications to a node/worker
- Controller Manager: maintains the cluster, handle node failures, number of pods, etc.
- etcd: is data store for cluster configuration.

The master node will never run applications on it, only worker nodes, however master node can be replicated for high availability purposes.


## Worker node

Is responsible for running the application and services you need:

- kubelet: runs and manage the containers on the node. It talks to the API server. It also talks to the container runtime (Docker)
- kubeproxy: load balance traffic between application components.
- container runtime: the program that runs the containers such as Docker (there are others rkt, containerd).

## API

All the components talk directly to the API server and it's the only one able to talk to `etcd` (configuration). We can get the status of the components with
the following command:

```
$ kubectl get componentstatus
```

you can inject more options to `kubectl` passing a `yaml` file during the call (limitation of using the command), also it's useful to version the configuration files in `git`. Consider the following `deployment.yaml` file:

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

- `replicas`: parameter tell the cluster to keep all the times 2 pods/instances running the whole time.
- `apiVersion`: is the version of Kubernetes API to use, v1 is the current one. Software and API version may differ.
- `kind`: type of object you want to deploy. previous example is `Deployment` but can be `Pod`, `DaemonSet` and so forth.
- `metadata`: Unique data that helps to identify an object. UID must be unique, there can't be two Pods running with the same **NAME**. Names can't be over 253 characters.
- `spec`: Defines the desire state of the object.
  - `spec/containers`: the containers Pod image, volumes, exposed ports for the container.
- `status`: Describe the actual state of the object.
- `labels`: You can add labels to add more information about the object and use them as selectors (apply an action). It works similar as AWS tags.
```
kubectl get pods --show-labels
```
- `annotations`: k,v can't use them as selectors. We can add more annotations to objects.


We can deploy the previous deployment with:

```
$ kubectl create -f deployment.yaml
```

Internally, `kubectl` converts the output from the previous configuration to JSON since this is the format the API server understands.


## References

[1] https://kubernetes.io/docs/concepts/overview/components/
[2] https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/
[3] https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/
[4] https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/
[5] https://kubernetes.io/docs/concepts/overview/components/#etcd
[6] https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/
[7] https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/
[8] https://kubernetes.io/docs/concepts/overview/components/#container-runtime
[9] https://kubernetes.io/docs/concepts/overview/kubernetes-api/#api-versioning
[10] https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#object-spec-and-status
[11] https://kubernetes.io/docs/concepts/overview/working-with-objects/field-selectors/
