
# 🧩 Feature Flag Based on Role – UI vs API Level Cheat Sheet

---

## 🔁 **1. Key Differences**

| Aspect              | UI-Level Feature Flags                         | API-Level Feature Flags                                        |
| ------------------- | ---------------------------------------------- | -------------------------------------------------------------- |
| **Visibility**      | Controls **display logic** (buttons, sections) | Controls **execution logic** (endpoint logic, data processing) |
| **Security**        | ❌ Not secure alone (can be bypassed)           | ✅ Secure (enforces access server-side)                         |
| **Latency**         | Fast (local flags or config fetch)             | Slightly slower (flag checked on server)                       |
| **Example**         | Hide “Download CSV” for basic users            | Block `/export` endpoint for non-admins                        |
| **Recommended for** | UX control                                     | Authorization and logic enforcement                            |

---

## 🌐 **2. UI-Level Role-Based Feature Flag**

### ✅ Approach:

* Use **JWT claims**, **role**, or **feature list** stored in browser (localStorage/context).
* Conditionally render components based on role.

### 💡 Example (React/Next.js):

```jsx
const user = getUserFromJWT(); // roles = ['USER'], features = ['can_export']

{user.features.includes('can_export') && (
   <button onClick={handleExport}>Export CSV</button>
)}
```

Or using role:

```jsx
{user.roles.includes('ADMIN') && <AdminPanel />}
```

### 🛠 Tools:

* Context API / Redux for feature state
* Remote flag services: LaunchDarkly, Unleash via SDK
* Local `.json` config (for static roles/flags)

---

## 🛡️ **3. API-Level Role-Based Feature Flag**

### ✅ Approach:

* Backend parses JWT, extracts roles/claims.
* Use **interceptors**, **filters**, or **feature flag service** to toggle behavior.

### 💡 Example (Spring Boot + JWT):

```java
@PreAuthorize("hasRole('ADMIN') and @featureService.isEnabled('EXPORT_CSV')")
@GetMapping("/export")
public ResponseEntity<?> exportCSV() { ... }
```

Or manually:

```java
if (user.hasRole("ADMIN") && featureService.isEnabled("EXPORT_CSV")) {
    return exportData();
} else {
    throw new AccessDeniedException();
}
```

### 🛠 Tools:

* Spring Security, Keycloak, FF4j, Togglz, Unleash
* Custom annotation: `@FeatureGate("EXPORT_CSV")`
* Role-to-feature mapping from config/DB

---

## 📊 **4. Role-to-Feature Mapping (Centralized)**

Store mapping in DB or Config:

```json
{
  "ADMIN": ["export", "beta_ui", "edit"],
  "USER": ["view", "comment"],
  "GUEST": ["view"]
}
```

---

## 🚦 Best Practices

| Practice                                                           | Reason                  |
| ------------------------------------------------------------------ | ----------------------- |
| ✅ Always validate features on **API side**                         | Prevent frontend bypass |
| ✅ Use **JWT claims** to send feature/role info                     | Avoid repeated lookups  |
| ✅ Use **remote config** to update flags without deploy             | Dynamic flag control    |
| ✅ Use feature flags for **gradual rollout** (per-role, per-region) | Safe deployments        |
| ✅ Combine **flags + roles** for fine-grained control               | Flexible access model   |

---

## 🔁 Combined Flow (UI + API):

```
1. Auth Server → JWT with roles + features
2. UI reads JWT → shows/hides features
3. API verifies role + feature before executing
4. Optional: Flags fetched from Unleash/LaunchDarkly
```
