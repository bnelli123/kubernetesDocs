# 🔐 Kubernetes API Server – Security BasicsThe **Kubernetes API Server** is the **central control point** of the cluster.  All communication — whether from users, pods, or controllers — goes through it.> 👉 If you secure the API Server, you secure your cluster.---## 🧠 What the API Server Does (Security Perspective)The API Server handles:1. **Authentication** → Who is making the request?2. **Authorization** → Are they allowed to do it?3. **Admission Control** → Should this request be allowed/modified?4. **Audit Logging** → What happened?---## 🔑 1. Authentication (Who are you?)The API Server verifies the identity of the caller.### Supported Methods- Client Certificates- Bearer Tokens (ServiceAccounts)- OIDC (SSO providers like Okta, Google)- Webhook Token Authentication- IAM (in EKS)### Example Flow```textkubectl → kubeconfig → API Server → Authenticated user
Key Point

❗ If authentication fails → request is rejected immediately


🛂 2. Authorization (What can you do?)
After authentication, the API Server checks permissions.
Common Authorizers


RBAC (most widely used)


ABAC (legacy)


Node Authorizer


Webhook


Example
User: dev-userAction: list podsNamespace: dev→ Allowed only if RBAC permits
Key Point

❗ No permission = request denied (even if authenticated)


🧪 3. Admission Controllers (Policy Enforcement)
Admission controllers run after authorization but before object creation.
What They Do


Validate requests


Mutate requests


Enforce policies


Examples


Pod Security Admission


OPA Gatekeeper


Kyverno


Example Use Cases


Block privileged containers


Enforce resource limits


Require labels



🧾 4. Audit Logging (Who did what?)
The API Server records all requests.
What Gets Logged


Who made the request


What action was performed


Resource affected


Timestamp


Why It Matters


Security investigations


Compliance


Debugging



🔒 5. Transport Security (TLS)
All communication with the API Server is encrypted.
Key Components


HTTPS (TLS) only


Certificates for:


API Server


Clients


etcd




Key Point

❗ Never expose API Server over insecure HTTP


🧱 6. API Server ↔ etcd Security
The API Server stores all cluster data in etcd.
Security Measures


TLS encryption between API Server and etcd


etcd access restricted (no public exposure)


Encryption at rest (recommended)



🚫 7. Common Security Risks
⚠️ Over-permissive RBAC


cluster-admin given unnecessarily


⚠️ Anonymous Access Enabled
--anonymous-auth=true
⚠️ Exposed API Server


Public endpoint without proper controls


⚠️ No Audit Logs


No visibility into actions


⚠️ Weak Authentication


Static tokens, no rotation



✅ 8. Best Practices


Disable anonymous access


Use RBAC with least privilege


Enable audit logging


Use OIDC / IAM for authentication


Restrict API Server access (private endpoint / firewall)


Enable encryption at rest


Regularly rotate certificates



🧠 Mental Model
Request → API Server → [AuthN] → [AuthZ] → [Admission] → etcd                          ↓                       [Audit Log]

🔑 Summary


API Server is the security gatekeeper


Every request passes through:


Authentication


Authorization


Admission control




Securing it = securing the entire cluster



🚀 Next Step


ServiceAccounts & RBAC (deep dive)


Admission Controllers (hands-on policies)


