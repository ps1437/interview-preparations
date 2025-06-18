# 🔐 **AuthN, AuthZ, Claims, Role-Based Feature Flags – Cheat Sheet**

---

## ✅ **1. Authentication (Who are you?)**

| Concept                     | Description                              | Example                         |
| --------------------------- | ---------------------------------------- | ------------------------------- |
| **JWT (JSON Web Token)**    | Stateless auth token with claims         | `Authorization: Bearer <token>` |
| **OAuth2**                  | Delegated auth (via Google, GitHub etc.) | `spring-security-oauth2-client` |
| **SSO (Single Sign-On)**    | One login across multiple apps           | Uses centralized IdP            |
| **IdP (Identity Provider)** | Auth service like Auth0, Keycloak, Okta  | Issues tokens                   |

**JWT Structure:**

```
Header.Payload.Signature
e.g., {
  "sub": "user123",
  "role": "ADMIN",
  "features": ["export", "edit"]
}
```

---

## 🔒 **2. Authorization (What are you allowed to do?)**

| Type                                      | Description                                    | Example                       |
| ----------------------------------------- | ---------------------------------------------- | ----------------------------- |
| **Role-Based Access Control (RBAC)**      | Based on roles like `ADMIN`, `USER`            | `hasRole('ADMIN')`            |
| **Attribute-Based Access Control (ABAC)** | Based on attributes/claims (age, region, dept) | `user.region == 'EU'`         |
| **Permission-based**                      | Fine-grained like `READ_USER`, `EDIT_MOVIE`    | More scalable than just roles |

**Spring Boot:**

```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin")
public String getAdminData() { ... }
```

---

## 🧾 **3. Claims (Metadata inside JWT)**

| Claim      | Purpose                         |
| ---------- | ------------------------------- |
| `sub`      | Subject (User ID)               |
| `iss`      | Issuer (e.g., Keycloak)         |
| `exp`      | Expiry timestamp                |
| `roles`    | List of user roles              |
| `features` | Optional: flags or entitlements |

**Custom Claims Example:**

```json
{
  "sub": "u123",
  "roles": ["USER", "EDITOR"],
  "features": ["can_export", "beta_feature_enabled"]
}
```

---

## 🚩 **4. Feature Flags (Role/Claim Based)**

| Feature Flag Type | Description                            | Example                                     |
| ----------------- | -------------------------------------- | ------------------------------------------- |
| **Boolean Flag**  | Toggle ON/OFF                          | `"feature_x": true`                         |
| **Role-based**    | Enabled for certain roles              | `"can_see_analytics": ["ADMIN", "MANAGER"]` |
| **Claim-based**   | Enabled by user attributes             | `"enable_beta": users from `IN\` region     |
| **Remote config** | Managed from service like LaunchDarkly | Dynamic toggling                            |

**Example Approaches:**

* **Backend (Spring):**

```java
if (user.hasRole("ADMIN") && featureService.isEnabled("featureX")) {
    // Enable feature logic
}
```

* **Frontend (React):**

```jsx
{features.includes('can_download') && <DownloadButton />}
```

* **With Unleash SDK / LaunchDarkly / Config Server:**

```java
if (unleash.isEnabled("recommendation_v2", userId)) {
   // Use new recommender
}
```

---

## 🧩 **Tech Stack Mapping**

| Requirement                    | Recommended Tool                              |
| ------------------------------ | --------------------------------------------- |
| Auth (JWT, OAuth2)             | Spring Security, Keycloak, Auth0              |
| Roles/Permissions              | Spring Security, Casbin                       |
| Feature Flags                  | Unleash, LaunchDarkly, FF4j, Togglz           |
| Claims in JWT                  | Use Auth provider (e.g., Auth0 custom claims) |
| Realtime role/flag propagation | Redis, WebSocket, polling                     |

---

## 🧠 **Best Practices**

* Store **roles and features as claims** in the JWT.
* Keep feature flags **outside of code** (use external service or config).
* **Cache permissions/flags per user** if checking frequently.
* **Log flag checks** to debug access issues.
* Use **circuit breaker/fallback** if flag service fails.
