# 🚀 ArgoCD-Installation-on-Ubuntu-22.04

![Argo-CD](https://github.com/user-attachments/assets/0894b78a-924a-4478-b1d3-126e6da3727a)

## 📌 Prerequisites

* A running Kubernetes cluster
* `kubectl` configured to access the cluster
* `jq` installed (`sudo apt install jq -y`)

---

## 🔧 Installation Steps

### 1️⃣ Create ArgoCD Namespace

```bash
kubectl create namespace argocd
```

### 2️⃣ Install ArgoCD using Official Manifest

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
```

---

## 🌐 Expose ArgoCD Server with LoadBalancer

By default, `argocd-server` is not exposed publicly.
Patch it to use a **LoadBalancer**:

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

---

## 🔑 Get ArgoCD Server URL

```bash
export ARGOCD_SERVER=$(kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname')
```

Check the external LoadBalancer IP/hostname:

```bash
echo $ARGOCD_SERVER
```

---

## 🔐 Get ArgoCD Admin Password

The initial password is stored in a Kubernetes secret.
Decode and export it:

```bash
export ARGO_PWD=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
```

Show the password:

```bash
echo $ARGO_PWD
```

---

## 👤 Default Credentials

* **Username:** `admin`
* **Password:** `$ARGO_PWD`

---

## 🧹 Cleanup (Optional)

If you want to delete the LoadBalancer for `argocd-server`:

```bash
kubectl delete svc argocd-server -n argocd
```

---

✅ You now have **ArgoCD** installed and exposed via LoadBalancer.

---
