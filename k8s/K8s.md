Kubernetes
==========

Kubernetes uses vitual box + docker machine to manage cluster

Install
-------

See [Official Instructions](https://github.com/kubernetes/minikube)

```
brew install minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-hyperkit \
&& chmod +x docker-machine-driver-hyperkit \
&& sudo mv docker-machine-driver-hyperkit /usr/local/bin/ \
&& sudo chown root:wheel /usr/local/bin/docker-machine-driver-hyperkit \
&& sudo chmod u+s /usr/local/bin/docker-machine-driver-hyperkit
```

Running
-------

```
minikube start --vm-driver=hyperkit
```

Terms
-----

-	Master - master node for managing cluster
-	Node - agent for performing work. Each node has kubelet and container engine (i.e. Docker)
-	Kubectl - CLI tool for interacting with k8s
-	Pod - Group of one or more containers and shome shared resources - volumes / networks etc. Containers in a Pod share IP and port space and run on same host.
-	Service - logical group of pods and policy by which to access them. Have an integrated load-balancer.
-	Deployment - provides a declartive update for Pods and ReplicaSets.
-	Lifecycle - pods have a lifecycle (pending, running, failed etc)
-	Container probe - liveliness and readiness checks
-	Replication controller - Make sure correct number of pod replicas are running
-	Label - key=value pair attached to objects. They are not unique. Can be created / modified / attached at any time.
-	Label selector - used by client/user to identify a set of objects. Empty label selector selects all.
-	Service spec - define a service including how to be exposed, IP / Port / Load balancer / name
-	Rolling update - deployment with zero downtime.
