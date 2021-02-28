# Certified Kubernetes Administrator notes

## Basic components

**Master**

* EtcD cluster / state
* Kube Scheduler (schedules applications)
* Kube Controller Manager (manages all controllers; node, replication etc...)
* Kube API server (orchestrates cluster operations)

**Worker**

* Kublet (listens to instructions from Kube API server)
* Kube-proxy manipulates `iptables` to enable communications between services in the cluster

### EtcD

* Key-value store
* Port 2379 by default
* Stores all the state for Kubernetes
* `etcctl` - CLI to managed cluster

#### Basic Flow

1. `api-server` receives request for new desired state
2. state is stored in `etcd`
3. `kube-scheduler` watches `api-server`, decides on next actions, informs `api-server`
4. state is stored in `etcd`
5. `kubelet` observes change in `api-server`, performs action, updates `api-server`

---

### api-server

* authentication
* validation
* **only** thing to update `etcd`
* can be deployed as a pod, or external cluster (https://betterprogramming.pub/a-closer-look-at-etcd-the-brain-of-a-kubernetes-cluster-788c8ea759a5)

---

### Controller manager

* process that continuously monitors the state of the system and remediates it
* all controllers logically separate, but physically deployed as one binary (node, replication, deployment, StatefulSet, jobs, etc...)
* `kube-controller-manager` deployed as a pod on master node (is it always?)

---

### kube-scheduler

* decides which pod goes on which node (`kubelet` actions this)
* looks at each pod and tries to find "best" node
  * filters out "unfit" nodes
  * uses priority function to choose best from remaining nodes

---

### kubelet

* loads / unloads pods
* reports status of pods to `api-server`

---

### kube-proxy

* every pod can communicate with every pod via IP
* a `service` provides a name to a pod
* runs on each node in the cluster
* creates rules (typically `iptables`) to forward service IP traffic to the pod IP
* deployed as a `DaemonSet`

---

## Objects

Kubernetes objects are persistent entities in the Kubernetes system [official docs](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)

### Pod

* single instance of an application
* can contain multiple containers (e.g. helper containers / sidecars)
* shared network and process namespaces
* not publicly accessible to external entities without explicit ennoblement via another object

```
apiVersion: v1
kind: Pod
metadata:
  name: ...
spec:
  containers: []
```

## ReplicaSet

* replication controller makes sure replicas are running
* `ReplicaSet` requires `selector`
* Older technology replaced was called `ReplicationController`

## DaemonSets

* Similar format to ReplicaSets
* uses taints and affinity to schedule

## Deployments

* Level of abstraction above `ReplicaSet` in object hierarchy
* Handles rolling updates/operational aspects \(life-cycle\)
* When a deployment is updated a rollout is triggered
  * use strategies to control deployments
    * `recreate` kill all, then create new pods
    * `rolling update` **default** - one-by one. uses `ReplicaSet` to perform the rollout
  * `kubectl rollout status deploy/...`
  * `kubectl rollout undo deploy/...` to switch to previous `ReplicaSet`

## Namespaces

* Group resources into logical partitions
* Some built-in default namespaces
  * kube-system - k8s internal resources
  * kube-public - resources for all users from any namespace to access
  * default - used if not explicitly specified
* can have resources allocated (e.g. `ResourceQuota`)
* It forms one components of kubernetes host DNS names
  * `<name><namespace>.svc.cluster.local` \(`cluster.local` is the default domain that can be configured\)
* An object can be assigned to a namespace via the `metadata.namespace` property
* create with `Kind: Namespace`

## Services

* Expose resources to external entities, can have different `spec.type` values:
  * `NodePort` - port on node is forwarded to a pod
  * `ClusterIP` virtual IP for service. Not externall accessible.
  * `LoadBalancer` - use external infrastructure to handle ingress into k8s cluster. If `LoadBalancer` is not supported then `NodePort` will be used.
* By default, will load balance traffic across matching pods using a random distribution algorithm

## Secrets

* Stored in an encode/hashed format
* `kubectl create secret generic <name> --from-literal=<key>=<value>` or `--from-file=<path>`
* If using `YAML` to create secret then data must be encoded in file (base64)
* `spec.containers.envFrom.secretKeyRef` or `volumes.secret.secretName`
* **Secrets are not very safe**

---

# Scheduling

## Controlling Scheduling

* Can manually schedule a pod on a node by setting the `spec.nodeName` property (creates a "Binding" between the pod and node)
* Labels are used for selecting objects
* Annotations can attach arbitrary data such as build information. Mainly used by integrations / kubernetes system
* Taints / Tolerations - what pods can be scheduled on what nodes
  * By default pods have zero tolerations
  * `kubectl taint nodes key=value:effect` (effect can be `NoSchedule`, `NoExecute`, `PreferNoSchedule`)
  * define in `spec.tolerations`
  * taints - restrict what can be placed but does not guarantee that a pod will be scheduled to a given node (that is node affinity)

### Node affinity

* node selectors - `spec.nodeSelector`. Must mat a label on the node. Just a simple selector
* node affinity - `spec.affinity`. can define a complex rules such as "in" and "not in"

---

## Resource requirements

* there are some default requirement assumptions
  * defined by LimitRange - 0.5CPU / 256Mb RAM
* `spec.containers.[].resources`
* CPU is throttled
* Memory is a hard limit. Pod is terminated by system watchdog

---

## Static Pods

* defined in a static file in a pre-defined directory (`--pod-manifest-path`)
* the `kubelet` will look in this location and automatically create them
* the `api-server` **is** aware of static pods (read-only in `api-server` via `kubectl`)
* useful for deploying control-plane elements
* static pods are scheduled on the node where they are defined

## Multiple schedulers

* multiple schedulers can be active at the same time
  * selected with the `--scheduler-name` parameter in the `kube-scheduler` binary
* beware of subtleties between multiple masters / single master setups
* `spec.schedulerName` to use non-default scheduler
* `kubectl get events` / `kubectl logs <scheduler>` to view custom scheduler information
* `kubectl describe <pod>` - show what it scheduled

## Multi-container pods

* sidecar
* adapter
* ambassador

## init containers

* In a multicontainer pod, each pod has to stay alive
* an init container exits after it completes
* after init container exits, the main containers can then start
* pods will be waiting until the init containers finish
* multiple init-containers are executed sequentially in the order they are defined


---

# Operating

## Metrics server

[official docs](https://github.com/kubernetes-sigs/metrics-server)

* No built-in solutions, but metrics-server can gather container resource metrics and expose them
* Aggregates data and stores them in memory
* Uses cAdvisor included in the kubelet
* `kubectl top <pod|node>` to view

## Application logs

* `kubectl logs [-f] <pod> [container name]`

---

## Upgrades

* operations:
  * **drain** - reschedules all pods on other nodes
    * `--ignore-daemonsets` flag
  * **uncorden** - marks a node as schedule-able
  * **cordon** - no more pods can be scheduled on node
* cannot drain if there is a `Pod` without a `ReplicaSet`
