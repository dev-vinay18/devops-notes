# ☸️ Kubernetes Learning Notes

Personal notes on setting up an Amazon EKS cluster and working with Pods & Services.

---

## 📅 Day 1 — Creating a Kubernetes Cluster on Amazon EKS

### 🔧 Steps to create the cluster

1. **Go to Cluster → Create Cluster**
2. Select **Custom configuration**
3. **Turn off** Auto EKS mode
4. Give your **cluster a name**
5. **Create a new IAM role**
   - Click **New Role** → it auto-generates the required configuration
   - Click **Create Role**
   - Click **Next**
6. **Specify Networking**
   - Add a **Security Group**
   - Click **Next**
7. Leave all **other configurations as default** → Click **Create** ✅

### 💻 Set up an EC2 instance to manage the cluster

8. Launch an EC2 instance (instance type: **c7i**)
9. Use the Kubernetes setup repo (provided by Rohit Sir) and run the given commands on the EC2 instance — up to (and including) `aws configure`

### ⚙️ Add a Node Group (worker nodes)

10. Go to your **Cluster → Compute (tab) → Add Node Group**
11. Give the node group a **name**
12. **Create a new IAM role**
    - Click **Auto-generate**
    - Click **Create**
    - Click **Next**
13. Click **Next → Next** → **Create** 🎉

### ✅ Final setup

14. Go back to the Kubernetes repo and run the **remaining commands** provided by Rohit Sir to finish connecting `kubectl` to your new cluster.

---

## 📅 Day 2 — Creating Pods & Services

### 🐳 1. Create a Pod

Create a YAML file for the pod (e.g. `pod.yaml`):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

Apply it on the EC2 instance:

```bash
kubectl apply -f pod.yaml
```

Check your pods:

```bash
kubectl get pods
```

For more detailed info:

```bash
kubectl get pods -o wide
```

---

### 🌐 2. Create Services for the Pod

There are **3 main types of Services** in Kubernetes:

| Type | Scope | Use Case |
|------|-------|----------|
| 🔒 **ClusterIP** | Internal only | Pod-to-pod communication within the cluster |
| 🚪 **NodePort** | Internal + External | Dev/testing — exposes a port in range `30000–32767` |
| ☁️ **LoadBalancer** | Internal + External | Production — requires a cloud provider (AWS, GCP, etc.) |

---

#### 🔒 ClusterIP Service

Create `clusterip.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-service
spec:
  selector:
    app: nginx
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Apply it:

```bash
kubectl apply -f clusterip.yaml
```

> ℹ️ Only reachable **from within the cluster** — great for internal pod-to-pod traffic.

---

#### 🚪 NodePort Service

Create `nodeport.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 31000
```

Apply it:

```bash
kubectl apply -f nodeport.yaml
```

Then open in your browser:

```
http://<node-server-ip>:31000
```

---

#### ☁️ LoadBalancer Service

Create `loadbalancer.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadbalancer-service
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Apply it:

```bash
kubectl apply -f loadbalancer.yaml
```

Get the external DNS/IP:

```bash
kubectl get svc loadbalancer-service
```

Copy the **EXTERNAL-IP / DNS name** and paste it into your browser 🌍

---

## 🎯 Quick Recap

- ✅ Day 1 → Built an EKS cluster + connected `kubectl` via EC2
- ✅ Day 2 → Deployed a Pod and exposed it using **ClusterIP**, **NodePort**, and **LoadBalancer** services

---

📌 *Notes based on a hands-on Kubernetes + AWS EKS session.*
