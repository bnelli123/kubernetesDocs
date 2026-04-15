# 🔐 Kubernetes API Server – Security Overview

## 📌 What is the API Server?

The **Kubernetes API Server** is the **front-end of the control plane**.

It is responsible for:

- Exposing the Kubernetes API (used by `kubectl`, controllers, and applications)
- Processing all cluster requests, such as:
  - Creating Pods
  - Reading Secrets
  - Deleting Deployments

---

## 🔑 Why the API Server is Critical for Security

Every request in Kubernetes must pass through multiple **security layers** enforced by the API Server.

---

## 🛂 1. Authorization (What can you do?)

After identity is verified, the API Server checks **permissions**.

### 🔹 Common Authorization Modes

- **RBAC** (most widely used)
- **ABAC** (legacy)
- **Node Authorization**

### 🔹 Example

- User tries to list pods  
- API Server checks:
  - Role / ClusterRole  
  - RoleBinding / ClusterRoleBinding  

---

## 🧪 2. Admission Control (Should this request be allowed?)

Even if a request is authorized, it is still **validated or modified** before execution.

### 🔹 Types

- **Validating Admission Controllers**
- **Mutating Admission Controllers**

### 🔹 Example Use Cases

- Block privileged containers  
- Enforce required labels  
- Inject sidecars automatically  

---

## 🔒 3. Secure Communication (TLS)

All communication with the API Server is **encrypted**.

### 🔹 Key Points

- Uses **HTTPS (TLS)**  
- Certificates ensure:
  - Confidentiality  
  - Integrity  

---

## 🧾 4. Audit Logging

The API Server logs all activity for visibility and compliance.

### 🔹 Logs Include

- Who made the request  
- What action was performed  
- When it happened  

---

## 🔐 5. Secrets Access Control

The API Server controls access to sensitive data:

- Secrets  
- ConfigMaps  

### 🔹 Access is enforced via

- RBAC  
- Admission policies  

---

## 🤖 6. ServiceAccount Integration

- Pods authenticate using **ServiceAccount tokens**  
- API Server validates these tokens  

---

## 🌐 7. Network Exposure

The API Server can be exposed in different ways:

- **Public endpoint** → Less secure  
- **Private endpoint** → Recommended  

### 🔹 Best Practices

- Restrict access via IP / VPC  
- Use private clusters (EKS)  

---

## 🧠 Request Flow (Security Pipeline)

- User/Pod Request  
  ↓  
- Authentication (Who?)  
  ↓  
- Authorization (Allowed?)  
  ↓  
- Admission Control (Safe?)  
  ↓  
- etcd (Data stored/retrieved)  

---

## ⚠️ Common Security Risks

- Over-permissive RBAC  
- Public API Server exposure  
- No audit logging  
- Misuse of default ServiceAccount  
- Weak authentication (no OIDC/IAM)  

---

## ✅ Best Practices

- Use **RBAC with least privilege**  
- Enable **audit logging**  
- Restrict API access (private endpoint)  
- Use **OIDC / IAM (EKS)**  
- Disable anonymous access  
- Rotate certificates regularly  

---

## 🔗 Key Takeaway

> The API Server is the **single entry point** for all Kubernetes operations.

It enforces:

- Identity (**Authentication**)  
- Access (**Authorization**)  
- Policy (**Admission Control**)  

👉 Securing the API Server = securing the entire cluster
