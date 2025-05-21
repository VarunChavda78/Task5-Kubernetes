Awesome! Task 5 is a great milestone for your DevOps journey — setting up a Kubernetes cluster locally and deploying apps using Minikube is foundational to learning Kubernetes.

Let’s do a **deep dive** on this task step-by-step, starting from zero on **Windows**. I’ll explain every step clearly, including commands, YAML structure, and how to verify.

---

# TASK 5: Build a Kubernetes Cluster Locally with Minikube (Windows) — Deep Dive

---

## 1. What is Minikube?

* **Minikube** is a tool that lets you run Kubernetes locally on your laptop.
* It runs a single-node Kubernetes cluster inside a VM or container on your machine.
* Perfect for learning, development, and testing without needing a full cloud setup.

---

## 2. Prerequisites (on Windows)

* **Windows 10 or higher** (with Hyper-V or VirtualBox enabled) or Windows Subsystem for Linux (WSL2)
* Install these tools:

### a) Install Docker Desktop for Windows

* Docker Desktop includes Docker Engine and Kubernetes support.
* [Download Docker Desktop](https://www.docker.com/products/docker-desktop/)
* Make sure Docker Desktop is running.
* Kubernetes can be enabled inside Docker Desktop, but for this task, we will use Minikube.

### b) Install Minikube

* Download Minikube Windows installer from [Minikube Releases](https://github.com/kubernetes/minikube/releases)
* Or install via Chocolatey (Windows package manager):

```powershell
choco install minikube
```

* Verify Minikube installation:

```powershell
minikube version
```

### c) Install kubectl (Kubernetes CLI)

* `kubectl` is the command-line tool to interact with Kubernetes.
* Install kubectl with Chocolatey:

```powershell
choco install kubernetes-cli
```

* Verify kubectl:

```powershell
kubectl version --client
```

---

## 3. Start Minikube Cluster

Open **PowerShell or Command Prompt** as Administrator:

```powershell
minikube start --driver=docker --image-repository="registry.aliyuncs.com/google_containers"
```

* This will start a local Kubernetes cluster using the default VM driver (Hyper-V or Docker driver).
* To specify the driver explicitly (e.g., docker):

```powershell
minikube start --driver=docker
```

* You should see output indicating Kubernetes components being started.

---

## 4. Verify Kubernetes Cluster Status

Check the cluster info:

```powershell
kubectl cluster-info
```

Check nodes (you should see one node named `minikube`):

```powershell
kubectl get nodes
```

---

## 5. Create a Deployment YAML File (deployment.yaml)

A **Deployment** defines your application pods and manages their lifecycle.

Create a file named `deployment.yaml` with the following example to deploy an Nginx web server:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

**Explanation:**

* `replicas: 2` means 2 pods of Nginx will run.
* `selector` tells which pods belong to this deployment.
* `template` describes the pod to create, with the nginx container listening on port 80.

---

## 6. Apply Deployment to Cluster

Run this command to create the deployment:

```powershell
kubectl apply -f deployment.yaml
```

You should see:

```
deployment.apps/nginx-deployment created
```

---

## 7. Check Pods Status

List pods running:

```powershell
kubectl get pods
```

You should see two pods with names like `nginx-deployment-xxxxxxx` and their status as `Running`.

---

## 8. Expose Deployment with a Service (service.yaml)

To access your app, create a Service to expose the Deployment.

Create a file `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30007
```

**Explanation:**

* `type: NodePort` exposes the service on a port of the node (minikube VM).
* `port` is the port inside the cluster.
* `targetPort` is the port in the container.
* `nodePort` is the port on your local machine to access (in range 30000-32767).

---

## 9. Apply Service

```powershell
kubectl apply -f service.yaml
```

Output:

```
service/nginx-service created
```

---

## 10. Access the App

Use Minikube to find the URL to access the app:

```powershell
minikube service nginx-service --url
```

This will output something like:

```
http://192.168.49.2:30007
```

Open this URL in your browser; you should see the default Nginx welcome page.

---

## 11. Scale the Deployment

Scale your deployment to 4 pods:

```powershell
kubectl scale deployment nginx-deployment --replicas=4
```

Verify pods:

```powershell
kubectl get pods
```

You will now see 4 pods running.

---

## 12. Get Detailed Info & Logs

Describe deployment:

```powershell
kubectl describe deployment nginx-deployment
```

Describe pods:

```powershell
kubectl describe pod <pod-name>
```

Get logs from a pod:

```powershell
kubectl logs <pod-name>
```

---

## 13. Cleanup

To delete resources:

```powershell
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

To stop minikube:

```powershell
minikube stop
```

To delete minikube cluster:

```powershell
minikube delete
```

---

# Summary

| Step                   | Command/File                                             | Description                    |
| ---------------------- | -------------------------------------------------------- | ------------------------------ |
| Install Minikube       | `choco install minikube`                                 | Install minikube locally       |
| Start cluster          | `minikube start`                                         | Starts Kubernetes cluster      |
| Create Deployment file | `deployment.yaml`                                        | Defines pods and containers    |
| Apply deployment       | `kubectl apply -f deployment.yaml`                       | Creates deployment in cluster  |
| Check pods             | `kubectl get pods`                                       | Verify pods are running        |
| Create Service file    | `service.yaml`                                           | Expose pods to outside network |
| Apply service          | `kubectl apply -f service.yaml`                          | Creates service                |
| Access service         | `minikube service nginx-service --url`                   | Opens app in browser           |
| Scale deployment       | `kubectl scale deployment nginx-deployment --replicas=4` | Scale pods count               |
| Inspect details & logs | `kubectl describe` / `kubectl logs`                      | Check pod/deployment details   |

---

If you want, I can also generate the full YAML files for you, or explain any step in more detail with diagrams or real-world analogies.

Would you like me to help you with that? Or do you want me to guide you through executing the commands live?
