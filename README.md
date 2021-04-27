# Kubernetes for Developers: Core Concepts

This is all about the k8s for beginners.

## Kubernetes from a Developer Perspective

![Communcating with kubeclt](./imgs/CommunicatingWithKubectl.png)
![K8S nodes](./imgs/KubernetesNodes.png)

### Installing & Running Kubernetes

We can use Minikube for local running K8S or Docker Desktop which is the easiest way to use it. It
just has one master node along with one worker node, but it is good enough.

Go to Docker preferences > Kubernetes > check the enable Kubernetes > click on Install button

### kubectl commands

We use kubeclt command line to interact with master node.

- `kubectl version`
- `kubectl cluster-info` view cluster information
- `kubectl get all` retreive infomration about Kubernetes Pods, Deployments, Services & more
- `kubectl run [container-name] --image=[image-name]` simply way to create a Deployment for a Pod
- `kubectl port-forward [pod] [ports]` forward a port to allow external access
- `kubectl expose ...` expose a port for a deployment/pod
- `kubectl create [resource]` create a resource
- `kubectl apply [resource]` create or modify a resource if already exists

YML file:

```
apiVersion: v1      > Kubernetes API version
kind: Pod           > Type of Kubernetes resource
metadata:           > Metadata about the Bod
  name: my-nginx
spec:               > The spec/blueprint for the Pod
  containers:       > Information about the containers that will run in the Pod
  - name: my-nginx
    image: nginx:alpine
```

To run the yml file:

`kubectl create -f file.pod.yml --dry-run --validate=true` We use --dry-run to check whether all
is ok but it will not create anything. The --validate is a default value to be true.

It is better to use apply instead if the pod does not exist it will be created or updated

`kubectl apply -f file.pod.yml`

To create a pod via yml file:
`kubectl create -f nginx.pod.yml --save-config` The --save-config is to save pod configuration which
can be found in annotations section after running `kubectl get pod my-nginx -o yaml` -o stands for
output. Another way to get inside of pod we can run `kubectl describe pod my-nginx`. --save-config should be used always with the create verb.

To get into running container inside of pod we can do the same like in Docker
`kubectl exec [pod-name] -it sh`

We can also edit yml file with `kubectl edit -f nginx.pod.yml`
And delete a pod `kubectl delete -f nginx.pod.yml`

Updated with liveness & readiness YML file:

```
apiVersion: v1      > Kubernetes API version
kind: Pod           > Type of Kubernetes resource
metadata:           > Metadata about the Bod
  name: my-nginx
spec:               > The spec/blueprint for the Pod
  containers:       > Information about the containers that will run in the Pod
  - name: my-nginx
    image: nginx:alpine
  livenessProbe:
    httpGet:
      path: /index.html
      port: 80
    initialDelaySeconds: 15
    timeoutSeconds: 2
    periodSeconds: 5
    failureTreshold: 1
  readinessProbe:
    httpGet:
      path: /index.html
      port: 80
    initialDelaySeconds: 15
    periodSeconds: 5

```
