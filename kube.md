Here’s a **clear and simple guide** to using **NGINX on Minikube**, including installation, deployment, exposure, and testing.

---

# ✅ **How to Use NGINX on Minikube**

Minikube lets you run Kubernetes locally. To run an NGINX server, you just need to deploy an NGINX Pod (or Deployment) and expose it.

---

# **Step 1: Start Minikube**

```bash
minikube start
```

Check status:

```bash
minikube status
```

---

# **Step 2: Deploy NGINX**

The simplest way:

```bash
kubectl create deployment nginx --image=nginx
```

Verify:

```bash
kubectl get deployments
kubectl get pods
```

Wait until the pod is **Running**.

---

# **Step 3: Expose NGINX Service**

Expose port **80** to access from your machine:

```bash
kubectl expose deployment nginx --type=NodePort --port=80
```

Check the service:

```bash
kubectl get svc
```

---

# **Step 4: Access NGINX in Browser**

Use Minikube to get the service URL:

```bash
minikube service nginx --url
```

It will output something like:

```
http://127.0.0.1:50045
```

Open it in your browser → You will see the **NGINX welcome page**.

---

# **Optional: Open the service UI automatically**

```bash
minikube service nginx
```

Automatically opens the browser window.

---

# **Step 5: View Logs (optional)**

```bash
kubectl logs -l app=nginx
```

---

# **Step 6: Edit NGINX Config (optional)**

To customize NGINX, create a ConfigMap:

```bash
kubectl create configmap nginx-config \
  --from-file=default.conf
```

Mount it in a Deployment YAML.

If you need this, tell me and I will generate the full YAML.

---

# **Step 7: Delete Everything**

```bash
kubectl delete svc nginx
kubectl delete deployment nginx
```

---

# Want to deploy NGINX with YAML?

I can give you a complete YAML file for:

✅ Basic NGINX
✅ NGINX with ConfigMap
✅ Ingress using NGINX Controller
✅ Deployment + Service + Ingress

Just tell me **your use case**!
Here you go — **scale NGINX to 4 replicas** and **verify the running deployment + pods**.

---

# ✅ **Step 1: Scale Deployment to 4 Replicas**

```bash
kubectl scale deployment nginx --replicas=4
```

---

# ✅ **Step 2: Check Deployment Status**

```bash
kubectl get deployment nginx
```

Expected output example:

```
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   4/4     4            4           10m
```

---

# ✅ **Step 3: List All Pods**

```bash
kubectl get pods -l app=nginx
```

Example output:

```
nginx-5c689d4bfb-abc12   1/1   Running   0   2m
nginx-5c689d4bfb-def34   1/1   Running   0   2m
nginx-5c689d4bfb-ghi56   1/1   Running   0   2m
nginx-5c689d4bfb-jkl78   1/1   Running   0   2m
```

Each pod will have a unique name but same ReplicaSet prefix.

---

# ✅ **Step 4: Describe Deployment (optional)**

```bash
kubectl describe deployment nginx
```

This shows strategy, pods, scaling events, and more.

---

# If you want, I can also show:

✔ How to autoscale with HPA
✔ YAML version of this scaled deployment
✔ How to expose via Ingress
✔ How to check logs from all pods

Just tell me!
