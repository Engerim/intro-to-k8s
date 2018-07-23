class: center, middle

# Introduction to Kubernetes

---

class: center, middle

<iframe width="560" height="315" src="https://www.youtube.com/embed/4ht22ReBjno" frameborder="0" allowfullscreen></iframe>

---

class: middle
background-image: url(https://i.kinja-img.com/gawker-media/image/upload/gf2yaqbwgtfeodvlyiga.jpg)
layout: true

---

.container-fluid[
## Kubernetes

* Open-source platform for automating deployment, scaling, and operations of application containers across clusters of hosts, providing container-centric infrastructure.
* The project was started by Google in 2014.
* Heavily influenced by Google's Borg system
* Kubernetes **is not** a traditional, all-inclusive PaaS (Platform as a Service) system.
]

---

.container-fluid[
## Kubernetes

* Kubernetes is a framework for building distributed systems.
* v1.0 released on 21.7.2015 and donated to CNCF (Cloud Native Computing Foundation)
* Latest version v1.11 (28.6.2018)
]

---

.container-fluid[
## Kubernetes

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">The Kubernetes project is hitting phase two; that&#39;s where the distros come in. Take your pick from Dies, OpenShift, Tectonic, GKE, and more.</p>&mdash; Kelsey Hightower (@kelseyhightower) <a href="https://twitter.com/kelseyhightower/status/775494220278738944">September 13, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
]

---

background-image: url(http://www.inquiriesjournal.com/article-images/uid-2647-1425259824/0740e1.jpg)

.container-fluid[
## Kubernetes

* Greek for _Helmsman_; also the root of the words _governor_ and _cybernetic_.
* **K8s** is an abbreviation derived by replacing the 8 letters _ubernete_ with 8.
]

---

class: middle
background-image: url(https://s-media-cache-ak0.pinimg.com/originals/a8/2e/2c/a82e2c9eda26ef4183a163007153c552.jpg)
layout: true

---

.container-fluid[
## Architecture

### The Kubernetes Node

#### `kubelet`

* The `kubelet` manages pods and their containers, their images, their volumes, etc.

#### `kube-proxy`

* A simple network proxy and load balancer.
* Can do simple TCP and UDP stream forwarding (round robin) across a set of backends.
]

---

.container-fluid[
## Architecture

### The Kubernetes Control Plane

#### `etcd`

* Distributed reliable key-value store.

#### Kubernetes API Server

* Intended to be a CRUD-y server, with most/all business logic implemented in separate components or in plug-ins.
* Mainly processes REST operations, validates them, and updates the corresponding objects in `etcd` (and eventually other stores).

#### Scheduler

* Binds unscheduled pods to nodes.

#### Kubernetes Controller Manager Server

* All other cluster-level functions are currently performed by the Controller Manager.
]

---

class: middle
background-image: url(http://www.coupofy.com/m/Store/content/ByUsf8fw.jpg)
layout: true

---

.container-fluid[
## Pods

.row[
.col-sm-6[
* A group of Docker containers with shared namespaces and shared volumes.
* Containers witih a pod share an IP address and port space, and can find each other via `localhost`.
]
.col-sm-6[
```
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```
]
]
]

---

background-image: url(http://images.nationalgeographic.com/wpf/media-live/photos/000/029/overrides/crypt-rome-italy_2947_990x742.jpg)

.container-fluid[
## Pods

> Kubernetes Pods are mortal. They are born they die, and they are not resurrrected.
]

---

class: middle
background-image: url(http://resource2.ultdb.net/res/org0011/HSS/14-04/201403311SAC.jpg)
layout: true

---


.container-fluid[
## Labels

.row[
.col-sm-6[
* Key/value pairs that are attached to objects, such as pods.
* Are intended to be used to specify identifying attributes of objects.
* Can be used to organize and to select subsets of objects.
* Can be attached to objects at creation time and subsequently added and modified at any time.
]
.col-sm-6[
```
metadata:
  labels:
    release: stable
    environment: dev
    tier: frontend
    partition: customerA
    track: daily
```
]
]
]

---


.container-fluid[
## Annotations

.row[
.col-sm-6[
* Not used to identify and select objects.
* The metadata in an annotation can be small or large, structured or unstructured, and can include characters not permitted by labels.
]
.col-sm-6[
```
metadata:
  annotations:
    key1: value1
    key2: value2
```
]
]
]

---

class: middle
background-image: url(https://upload.wikimedia.org/wikipedia/commons/c/c2/CN_Box_Car_Loader.JPG)
layout: true

---

.container-fluid[
## Label Selectors

* Via a *label selector*, the client/user can identify a set of objects.
* The API currently supports two types of selectors: *equality-based* and *set-based*.
* An empty label selector (that is, one with zero requirements) selects every object in the collection.
* A null label selector (which is only possible for optional selector fields) selects no objects.
]

---

.container-fluid[
## Label Selectors

### *Equality-based* requirements 

```
selector:
    component: redis
```

### *Set-based* requirements


```
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: NotIn, values: [dev]}
```
]

---

class: middle
background-image: url(http://i2.wp.com/nerdbastards.com/wp-content/uploads/2014/12/Rick-Deckard.jpg)
layout: true

---

.container-fluid[
## Replication Controllers

.row[
.col-sm-6[
* Ensures that a specified number of pod *replicas* are running at any one time.
* If there are too many pods, it will kill some.
* If there are too few, the replication controller will start more.
* The pods maintained by a replication controller are automatically replaced if they fail, get deleted, or are terminated.
]
.col-sm-6[
```
apiVersion: v1
kind: ReplicationController
metadata:
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        image: nginx
        ports:
        - containerPort: 80
```
]
]
]

---

class: middle
background-image: url(https://didyouseethatone.files.wordpress.com/2014/10/blade-runner-3.jpg)
layout: true

---

.container-fluid[
## Replica Sets

* The next-generation Replication Controller.
* Replica Set supports the new set-based selector requirements.
* Can also be a target for Horizontal Pod Autoscalers (HPA).
]

---

.container-fluid[
## Replica Sets

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
spec:
  replicas: 3
  selector:
    matchExpressions:
    - {key: app, operator: In, values: [nginx]}
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        image: nginx
        ports:
        - containerPort: 80
```
]

---

class: middle
background-image: url(http://images.nationalgeographic.com/wpf/media-live/photos/000/829/cache/army-deployment-plane-military_82981_990x742.jpg)
layout: true

---

.container-fluid[
## Deployments

* Provides declarative updates for Pods and Replica Sets (the next-generation Replication Controller).
* A typical use case is:
  * Create a Deployment to bring up a Replica Set and Pods.
  * Check the status of a Deployment to see if it succeeds or not.
  * Later, update that Deployment to recreate the Pods (for example, to use a new image).
  * Rollback to an earlier Deployment revision if the current Deployment isn’t stable.
  * Pause and resume a Deployment.
]

---

.container-fluid[
## Deployments

```
apiVersion: apps/v1
kind: Deployment
metadata:
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
]

---

class: middle
background-image: url(http://www.hotel-r.net/im/hotel/az/hotel-service-4.jpg)
layout: true

---

.container-fluid[
## Services

* A Kubernetes `Service` is an abstraction which defines a logical set of `Pod`s and a policy by which to access them - sometimes called **a micro-service**. 
* The set of `Pod`s targeted by a `Service` is (usually) determined by a Label Selector.
* `kube-proxy` is responsible for implementing a form of virtual IP for `Service`s other than `ExternalName`.
* Kubernetes supports multiple port definitions on a `Service` object.
* Kubernetes supports 2 primary modes of finding a `Service`: 
  * environment variables (`MY_NGINX_SERVICE_HOST=10.0.162.149`, `MY_NGINX_SERVICE_PORT=80`),
  * DNS (`my-nginx.default.svc.cluster.local`).
]

---

.container-fluid[
## Services

```
apiVersion: v1
kind: Service
metadata:
spec:
  ports:
    - port: 8765
      targetPort: 9376
  selector:
    app: example
```
]


---

.container-fluid[
## Services

* `ClusterIP`
  * Use a cluster-internal IP only.
  * Service to be reachable only from inside of the cluster.
* `NodePort`
  * On top of having a cluster-internal IP.
  * Expose the service on a port on each node of the cluster (the same port on each node).
* `LoadBalancer`
  * On top of having a cluster-internal IP and exposing service on a `NodePort` also, ask the cloud provider for a load balancer.
* `ExternalName`
  * Map the service to the contents of the `externalName` field (e.g. `foo.bar.example.com`), by returning a `CNAME` record with its value.
  * No proxying of any kind is set up. 
]

---

class: middle
background-image: url(http://1.bp.blogspot.com/-xihe-4kodOw/VWB3EdjWDuI/AAAAAAAABqc/AzrfX3mW5YY/s1600/proel%2Bmixer.jpg)
layout: true

---

.container-fluid[
## Volumes

* A volume is just a directory, possibly with some data in it, which is accessible to the containers in a pod.
* Has an explicit lifetime - the same as the pod that encloses it.
* Kubernetes supports many type of volumes, and a Pod can use any number of them simultaneously.
]

---

.container-fluid[
## Volumes
.row[
.col-sm-6[
* `emptyDir`
* `hostPath`
* `gcePersistentDisk`
* `awsElasticBlockStore`
* `nfs`
* `iscsi`
* `flocker`
* `glusterfs`
* `rbd`
]
.col-sm-6[
* `cephfs`
* `gitRepo`
* `secret`
* `persistentVolumeClaim`
* `downwardAPI`
* `azureFileVolume`
* `azureDisk`
* `vsphereVolume`
* `Quobyte`
]
]
]

---

.container-fluid[
## Volumes

```
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:
    image: redis
    volumeMounts:
      mountPath: /data/redis
  volumes:
    emptyDir: {}
```
]

---

class: middle
background-image: url(http://previews.123rf.com/images/dimol/dimol1206/dimol120600066/13993304-Old-vintage-retro-golden-compass-on-ancient-map-Stock-Photo.jpg)
layout: true

---

.container-fluid[
## Config Maps

.row[
.col-sm-6[
* The `ConfigMap` API resource holds key-value pairs of configuration data.
* Can be consumed in pods or used to store configuration data for system components such as controllers. 
* `ConfigMap` is similar to Secrets, but designed to more conveniently support working with strings that do not contain sensitive information.
]
.col-sm-6[
```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: default
data:
  special.how: very
  special.type: charm
```
]
]
]

---

.container-fluid[
## Config Maps

ConfigMaps can be used to:
1. Populate the value of environment variables.
2. Set command-line arguments in a container.
3. Populate config files in a volume.
]

---

.container-fluid[
## Config Maps

### Consume `ConfigMap` in environment variables

```
containers:
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "env" ]
    env:
        - name: SPECIAL_LEVEL_KEY
        valueFrom:
          configMapKeyRef:
            key: special.how
        - name: SPECIAL_TYPE_KEY
        valueFrom:
          configMapKeyRef:
            key: special.type
```
]

---

.container-fluid[
## Config Maps

### Set command-line arguments with `ConfigMap`

```
containers:
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
    env:
        - name: SPECIAL_LEVEL_KEY
        valueFrom:
          configMapKeyRef:
            key: special.how
        - name: SPECIAL_TYPE_KEY
        valueFrom:
          configMapKeyRef:
            key: special.type
```
]

---

.container-fluid[
## Config Maps

### Consume `ConfigMap` via volume plugin


```
containers:
    image: gcr.io/google_containers/busybox
    command: [ "/bin/sh", "-c", "cat /etc/config/special.how" ]
    volumeMounts:
      mountPath: /etc/config
volumes:
    configMap:
```
]

---

class: middle
background-image: url(http://blog.marketo.com/wp-content/uploads/2013/09/telling-secrets-big-e1379620235254.jpg)
layout: true

---

.container-fluid[
## Secrets

.row[
.col-sm-6[
* Are intended to hold sensitive information, such as passwords, OAuth tokens, and SSH keys
* Kubernetes automatically creates secrets which contain credentials for accessing the API and it automatically modifies your pods to use this type of secret.
* Can be mounted as data volumes or be exposed as environment variables to be used by a container in a pod.
]
.col-sm-6[
```
apiVersion: v1
kind: Secret
metadata:
type: Opaque
data:
  password: MWYyZDFlMmU2N2Rm
```
]
]
]

---

.container-fluid[
## Secrets

### Using Secrets as Environment Variables

```
containers:
    image: redis
    env:
        - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            key: username
        - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            key: password
```
]

---

class: middle
background-image: url(http://photos1.blogger.com/blogger/6915/2218/1600/lost%20three%20minutes%20station%20of%20the%20window.jpg)
layout: true

---

.container-fluid[
## Others

* `DaemonSet`
* `HorizontalPodAutoscaler`
* `Job`
* `NetworkPolicy`
* `PersistentVolume`
* `PersistentVolumeClaim`
* `PetSet`
* `ServiceAccount`
* `ThirdPartyResource`
* ...
]

---

background-image: url(http://orullian.com/testpress/wp-content/uploads/2012/10/wallpaper-650912.jpg)

---

layout: true
