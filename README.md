<div align="center">
<h1>🚀 Kubernetes Basics</h1>
<p><strong>Built with ❤️ by <a href="https://github.com/atulkamble">Atul Kamble</a></strong></p>

<p>
<a href="https://codespaces.new/atulkamble/template.git">
<img src="https://github.com/codespaces/badge.svg" alt="Open in GitHub Codespaces" />
</a>
<a href="https://vscode.dev/github/atulkamble/template">
<img src="https://img.shields.io/badge/Open%20with-VS%20Code-007ACC?logo=visualstudiocode&style=for-the-badge" alt="Open with VS Code" />
</a>
<a href="https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/atulkamble/template">
<img src="https://img.shields.io/badge/Dev%20Containers-Ready-blue?logo=docker&style=for-the-badge" />
</a>
<a href="https://desktop.github.com/">
<img src="https://img.shields.io/badge/GitHub-Desktop-6f42c1?logo=github&style=for-the-badge" />
</a>
</p>

<p>
<a href="https://github.com/atulkamble">
<img src="https://img.shields.io/badge/GitHub-atulkamble-181717?logo=github&style=flat-square" />
</a>
<a href="https://www.linkedin.com/in/atuljkamble/">
<img src="https://img.shields.io/badge/LinkedIn-atuljkamble-0A66C2?logo=linkedin&style=flat-square" />
</a>
<a href="https://x.com/atul_kamble">
<img src="https://img.shields.io/badge/X-@atul_kamble-000000?logo=x&style=flat-square" />
</a>
</p>

<strong>Version 1.0.0</strong> | <strong>Last Updated:</strong> January 2026
</div>

Below is a **very basic, beginner-friendly Kubernetes (K8s) cheat sheet** with **essential commands + minimal YAML templates**

![Image](https://images.clickittech.com/2020/wp-content/uploads/2022/04/13202329/Diagram-55.jpg)

![Image](https://iximiuz.com/kubernetes-vs-age-old-infra-patterns/kubernetes-service-min.png)

![Image](https://phoenixnap.com/kb/wp-content/uploads/2021/04/full-kubernetes-model-architecture.png)

---

## 1️⃣ Basic `kubectl` Commands (Must-Know)

### 🔹 Cluster & Context

```bash
kubectl version
kubectl cluster-info
kubectl config get-contexts
kubectl config use-context <context-name>
```

### 🔹 Nodes

```bash
kubectl get nodes
kubectl describe node <node-name>
```

### 🔹 Pods

```bash
kubectl get pods
kubectl get pods -o wide
kubectl describe pod <pod-name>
kubectl delete pod <pod-name>
```

### 🔹 Namespaces

```bash
kubectl get ns
kubectl create ns dev
kubectl get pods -n dev
```

---

## 2️⃣ Very Basic Pod Template (pod.yaml)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

### ▶ Apply & Test

```bash
kubectl apply -f pod.yaml
kubectl get pods
kubectl port-forward nginx-pod 8080:80
```

➡ Open: `http://localhost:8080`

---

## 3️⃣ Deployment (Recommended instead of Pod)

### deployment.yaml

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
        image: nginx
        ports:
        - containerPort: 80
```

### ▶ Commands

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
```

---

## 4️⃣ Service (Expose App)

### service.yaml (NodePort – easiest for beginners)

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
  - port: 80
    targetPort: 80
    nodePort: 30007
```

### ▶ Commands

```bash
kubectl apply -f service.yaml
kubectl get svc
```

➡ Access:
`http://<NODE-IP>:30007`

---

## 5️⃣ Scale Deployment

```bash
kubectl scale deployment nginx-deployment --replicas=5
kubectl get pods
```

---

## 6️⃣ Logs & Exec (Very Important)

### Logs

```bash
kubectl logs <pod-name>
kubectl logs -f <pod-name>
```

### Exec into Pod

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

---

## 7️⃣ Delete Resources

```bash
kubectl delete pod nginx-pod
kubectl delete deployment nginx-deployment
kubectl delete svc nginx-service
kubectl delete -f deployment.yaml
```

---

## 8️⃣ One-Line Quick Runs (Super Basic)

```bash
kubectl run nginx --image=nginx
kubectl expose pod nginx --type=NodePort --port=80
kubectl get all
```

---

## 9️⃣ Minimal Folder Structure (Best Practice)

```text
k8s-basic/
├── pod.yaml
├── deployment.yaml
└── service.yaml
```

---

## 🔟 What You Should Practice First

✅ Pod vs Deployment
✅ Labels & Selectors
✅ Service types (ClusterIP → NodePort → LoadBalancer)
✅ Scaling
✅ Logs & Exec

---
Below is **very basic, beginner-friendly Kubernetes YAML** for each resource.
You can **copy–paste and apply directly**.
(No advanced fields, no production extras.)

---

## 1️⃣ Deployment (Nginx)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

---

## 2️⃣ Service (ClusterIP)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  type: ClusterIP
  selector:
    app: demo
  ports:
  - port: 80
    targetPort: 80
```

Apply:

```bash
kubectl apply -f service.yaml
```

---

## 3️⃣ ConfigMap (Simple Key-Value)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  APP_NAME: "MyDemoApp"
  ENV: "dev"
```

Use in Pod (example):

```yaml
env:
- name: APP_NAME
  valueFrom:
    configMapKeyRef:
      name: demo-config
      key: APP_NAME
```

---

## 4️⃣ Secret (Base64 Encoded)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: demo-secret
type: Opaque
data:
  username: YWRtaW4=      # admin
  password: MTIzNDU=      # 12345
```

> Encode value:

```bash
echo -n admin | base64
```

Use in Pod:

```yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: demo-secret
      key: username
```

---

## 5️⃣ Ingress (Very Basic)

> ⚠️ Requires **Ingress Controller** (like NGINX Ingress)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
spec:
  rules:
  - host: demo.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: demo-service
            port:
              number: 80
```

Test locally (optional):

```bash
curl -H "Host: demo.example.com" http://<INGRESS-IP>
```

---

## 6️⃣ HPA (CPU Based)

> ⚠️ Requires **metrics-server**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: demo-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: demo-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

Check:

```bash
kubectl get hpa
```

---

## 📁 Recommended File Structure

```
k8s-basic/
│── deployment.yaml
│── service.yaml
│── configmap.yaml
│── secret.yaml
│── ingress.yaml
│── hpa.yaml
```

---


