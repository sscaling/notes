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
  * `ClusterIP` virtual IP for service. Not externally accessible.
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

## Backup & Restore

* Declarative config can be committed to source-control
* `kubectl get all --all-namespaces -o yaml` to backup all config
* Tools such as Arc / Velero can help
* etcd:
  * stores all data in the "data dir"
  * can also take a snapshot of the etcd cluster
  * when you restore you can create a new data dir (and token) then update the service config and restart
  * *!Important* stop api-server during restore.

## Storage Drivers

* Storage drivers and volume drivers
* image layers are read-only
* container layer filesystem is last layer
  * this is read-write
  * uses CoW (Copy-on-Write)
* in docker create a volume (/var/lib/docker/volumes)
* can be mounted into a container
* can mount a host directory into container (e.g. volume-mount or bind-mount)
* new docker options is `--mount`
* storage drivers handle behaviours (e.g. aufs, zfs, btrfs, overlay etc)
* volume driver plugins (e.g. local, flocker, glusterfs)

## Container storage interface

* api for RPC calls
  * provision/destroy resources
  * should make the volume available

## K8s volumes

* a pods data is transient
* can mount a volume into a pod
* use `spec.containers.volume Mounts[[].mountPath.name`
* to define volume storage options; `spec.volumes[].name..` `hostPath / awsElasticBlockStore`

## Persistent volumes

* a fixed centralised volume defined by the adminstrator
* `kind: PersistentVolume`

## Persistent volume claims

* makes storage available to node
* user created
* a PVC is bound to a single volume
* can use labels/selectors
* 1:1 claim between PV and PVC (can waste resources)
* `kind: PersistentVolumeClaim`
* deleting; in the PV define `spec.persistentVolume.ReclaimPolicy...`
  * retain - PV remains until deleted
  * delete - PV deleted with PVC
  * recycle - data is scrubbed then made available for other PVCs
* in a pod - `spec.volumes[].persistentVolumeClaim.claimName`
* can be added to `ReplicaSets` or `Deployments`
* accessModes need to match
* if more storage available then claimed it is still accessible to the volume
* can't delete if used by a pod

---

# Security

## Primitives

* Start with a secure host
* `api-server`
  * authentication; files, external (LDAP etc), certificates
  * authorization; RBAC, ABAC, node authorizer, webhooks
  * TLS encryption for control-plane (certs); `api-server`, `scheduler`, `kubelet` etc
* use network policies to restrict communication between pods

## Authorization

* users could be; Admins, Devs, Bots, End-users
  * split into two groups; Humans, Machines
  * Machines can be managed with **service accounts**
* Kubernetes only supports **service accounts**
* auth mechanisms; flat-file, token file, certificates, identity services (LDAP, Kerberos etc)
  * configured in the `api-server` service

## Authorization mechanisms

* node-authorizer - authorises any requests from `system: node:...` users in the `system:nodes` group
* ABAC - Attribute Based Access Control
  * associate user or group with a set of permissions
  * create policy and restart `api-server`
* RBAC - Role Based Access Control
  * create a role defining permissions
  * assign user/group to that role
* webhook (e.g. Open Policy Agent)
  * external service grants or denies access
* AlwaysAllow / AlwaysDeny
  * use `--authorization-mode` on `api-server`
  * can be chained, e.g. Node, RBAC, webhook (on failure forward to next)

### RBAC

* Roles:
  * `Kind: Role`, `apiVersion: rbac.authorisation.k8s.io/v1`
  * `kind: RoleBinding`
  * `kubectl get roles|rolebindings`
  * `kubectl autho can-i <command>` (e.g. `create deployments`)
    * `--as <user>` to impersonate user
* ClusterRoles:
  * resources are either namespace or cluster scoped
  * `kind: ClusterRole` (same as `Role`, bind with `ClusterRoleBinding`)
  * Gives access to resources across all namespaces

## TLS

* `api-server` - TLS enabled
* etcd server - TLS enabled
* admins, scheduler, controller-manager, kube-proxy all have cert/keys for mTLS
* api-server <-> etcd use mTLS
* api-server <-> kubelet use mTLS
* generation; esrsa, openssl, cfssl etc
    * admin-user - `CN` and `OU` have meaning. (`OU=system:master`)
    * system component certs must be prefixed with `system:`
* **NOTE:** components may have different CAs

## Certificates API

* Send CSR to `certificates API`
  * Create CSR k8s object
   ```
   Kind: CertificateSigningRequest
   apiVersion: certificates.k8s.io/v1beta1
   ```
   **NOTE:** `spec.request` body must be base64 encoded
* `kubectl get csr`
* `kubectl certificate deny|approve <name>`
  * Once approved certificate is attached to CSR object for retrieval
* operations managed by the controller-manager (`--cluster-signing-[cert|key]-file`)

## Kubeconfig file

* clusters - different clusters that you have access to (specify hosts, CA etc)
* users - user accounts which you use (client certificates etc)
* context - which user config to use for which cluster
* `kind: Config`
* `kubectl config view|set context` / `kubectl config use-context`

## API groups

* core @ /api (no `s` at the end)
* named @ /apis (with `s`)
  * /apis/apps/v1
  * /apis/certificates.k8s.io/v1/certificatesigningrequest (Resource after API version)

## Network Policies

* defined with ingress & egress
* rules - `AllAllow`
* network policy bound to pod
  * bound by matching labels
* `kind: NetworkPolicy`, `spec.policyTypes[]` == ingress / egress
* network polices are enforced by the network solution used by the k8s cluster
  * can still create but they will not be enforced

## Image Security

* If no registry specified, it's assumed to be `docker.io`
* private registry:
  * `kubectl create secret docker-registry <name> <params>`
  * add `spec.imagePullSecrets.[]name` to pod

## Contexts

* `pod.spec.SecurityContext` or `pod.spec.containers[].SecurityContext`
  * `runAsUser` / `capabilites.add`

---

## Upgrades

* operations:
  * **drain** - reschedules all pods on other nodes
    * `--ignore-daemonsets` flag
  * **uncorden** - marks a node as schedule-able
  * **cordon** - no more pods can be scheduled on node
* cannot drain if there is a `Pod` without a `ReplicaSet`

## Cluster upgrades

* control plane components can have different versions
* all versions must be less-than or equal-to `api-server` @ X
  * controller manager / kube-scheduler X-1
  * kubelet / kube-proxy X-2
  * kubectl  X +/- 1
* recommended to do incremental upgrades (1.10 -> 1.11 -> 1.12 -> 1.13 etc). No jumps!
  * Process: (*NOTE*: upgrade kubelet on master node before workers)
    1. Upgrade master (management functions will be down, but running applications continue)
    2. Upgrade worker nodes
    3. goto (1)
* worker nodes can be upgraded with different strategies:
  * all at once
  * one-by-one
  * provision new nodes and decommission old nodes







