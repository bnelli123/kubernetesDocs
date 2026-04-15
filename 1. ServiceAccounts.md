# Kubernetes ServiceAccount

A **Kubernetes ServiceAccount** is a special type of account used by applications (pods) to interact with the Kubernetes API, rather than by human users.

---

## 🔹 What it is (Simple Explanation)

Think of a ServiceAccount as an **identity for a Pod**.

* 👤 Humans → use user accounts (`kubectl`, IAM, etc.)
* 🤖 Applications (inside Pods) → use **ServiceAccounts**

---

## 🔹 Why it’s Needed

Pods often need to:

* Read cluster state (e.g., list Pods, Services)
* Access ConfigMaps or Secrets
* Communicate with the Kubernetes API

A ServiceAccount provides:

* **Authentication** → Who the Pod is
* **Authorization** → What it can do (via RBAC)

---

## 🔹 Default Behavior

* Every namespace has a `default` ServiceAccount
* If you don’t specify one, Pods use it automatically

```bash
kubectl get serviceaccounts
```

---

## 🔹 How It Works Internally

When a ServiceAccount is assigned to a Pod:

1. Kubernetes creates a token
2. The token is mounted inside the Pod at:

   ```
   /var/run/secrets/kubernetes.io/serviceaccount
   ```
3. The Pod uses this token to authenticate with the API server

---

## 🔹 Example

### 1. Create a ServiceAccount

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
```

### 2. Assign it to a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: nginx
```

### 3. Grant Permissions (RBAC)

#### Create a Role

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

#### Bind Role to ServiceAccount

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
subjects:
- kind: ServiceAccount
  name: my-service-account
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

## 🔹 Are ServiceAccounts Bound to Pods?

👉 **Short Answer:** Yes — a Pod uses a ServiceAccount as its identity.

### How the Association Works

You assign a ServiceAccount like this:

```yaml
spec:
  serviceAccountName: my-service-account
```

Once the Pod is created:

* The ServiceAccount is attached to the Pod
* A token is mounted inside the Pod
* The Pod uses it to authenticate with Kubernetes

---

## 🔹 Important Clarifications

### ✅ Binding Happens at Pod Creation

* You **cannot change** the ServiceAccount of a running Pod
* To use a different one → recreate the Pod

### ✅ Multiple Pods Can Share One ServiceAccount

* One ServiceAccount can be used by many Pods

### ✅ Default ServiceAccount

If not specified:

```yaml
serviceAccountName: default
```

### ✅ Not the Same as RBAC Binding

* Pod ↔ ServiceAccount → **assignment (identity)**
* ServiceAccount ↔ Role → **permission binding (RBAC)**

---

## 🔹 Mental Model

| Concept        | Real-world analogy            |
| -------------- | ----------------------------- |
| Pod            | Employee                      |
| ServiceAccount | ID Badge                      |
| RoleBinding    | Permissions assigned to badge |

---

## 🔹 Key Points

* ServiceAccounts are **namespaced**
* Used by **Pods, not humans**
* Work with **RBAC (Role & RoleBinding)**
* Provide **secure API access**

---

## 🔹 Common Use Cases

* CI/CD jobs running inside the cluster
* Controllers / Operators
* Applications needing cluster metadata
* Secure access to Secrets

---

## 🔹 Summary

* Every Pod runs with a ServiceAccount
* ServiceAccounts provide identity to Pods
* Permissions are controlled using RBAC
* Multiple Pods can share one ServiceAccount
* Association is defined at Pod creation time

---

## 📌 Want to Learn More?

You can explore further topics like:

* ServiceAccount vs User
* Token types (legacy vs projected tokens)
* Security best practices (least privilege)
