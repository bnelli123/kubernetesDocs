# What is the API Server?

The API Server is the **front-end of the Kubernetes control plane**.

It:
- Exposes the Kubernetes API (used by `kubectl`, controllers, apps)
- Processes all requests such as:
  - Create Pod
  - Read Secrets
  - Delete Deployments

---

## 🔑 Why it’s critical for security

Every request must pass through these stages:

🔐 2. Authorization (What can you do?)

After identity is verified, API Server checks permissions.

Common authorization modes:
RBAC (most used)
ABAC (legacy)
Node authorization
Example:

User tries to list pods
API Server checks:

Role / ClusterRole
RoleBinding
🔐 3. Admission Control (Should this request be allowed?)

Even if authorized, the request is validated and possibly modified.

Types:
Validating Admission Controllers
Mutating Admission Controllers
Examples:
Block privileged containers
Enforce labels
Inject sidecars
🔐 4. Secure Communication (TLS)

All communication is encrypted.

API Server uses HTTPS (TLS)
Certificates ensure:
Confidentiality
Integrity
🔐 5. Audit Logging

Tracks all API activity.

Logs include:
Who made the request
What action was performed
When it happened
🔐 6. Secrets Access Control

API Server controls access to:

Secrets
ConfigMaps

👉 Access is enforced via:

RBAC
Admission policies
🔐 7. ServiceAccount Integration
Pods authenticate using ServiceAccount tokens
API Server validates these tokens
🔐 8. Network Exposure

API Server is exposed via:

Public endpoint (less secure)
Private endpoint (recommended)
Best practices:
Restrict access via IP / VPC
Use private clusters (EKS)
🧠 Request Flow (Full Security Pipeline)
User/Pod Request
        ↓
Authentication (Who?)
        ↓
Authorization (Allowed?)
        ↓
Admission Control (Safe?)
        ↓
etcd (Data stored/retrieved)
⚠️ Common Security Risks
Over-permissive RBAC
Public API Server exposure
No audit logging
Default ServiceAccount misuse
Weak authentication (no OIDC/IAM)
✅ Best Practices
Use RBAC with least privilege
Enable audit logs
Restrict API access (private endpoint)
Use OIDC / IAM (EKS)
Disable anonymous access
Rotate certificates regularly
🔗 Key Takeaway

The API Server is the single entry point for all operations —
controlling identity, access, and policy enforcement.
