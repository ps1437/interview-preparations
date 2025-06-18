# Feature Flag Enable/Disable Cheatsheet (Role-Based) 🛠️

This cheatsheet outlines how to **enable/disable feature flags** based on **user roles** for a PepsiCo project. It covers common scenarios, tools, and best practices for managing feature flags with Role-Based Access Control (RBAC).

---

## 📖 Overview
- **Feature Flags**: Toggle features ON/OFF without code redeployment.
- **RBAC**: Restricts flag access based on user roles (e.g., Admin, Developer, Product Manager).
- **Use Case**: Control feature visibility for PepsiCo's internal tools, apps, or customer-facing platforms.

---

## 🛠️ Common Tools
- **Flagsmith**: Open-source, supports RBAC for teams.
- **LaunchDarkly**: Enterprise-grade, role-based controls.
- **Unleash**: Flexible permissions for flag management.
- **Custom**: Database/config-based flags with role checks.

---

## 🎭 Role-Based Scenarios
| Role             | Permissions                              | Example Action                     |
|------------------|------------------------------------------|------------------------------------|
| **Admin**        | Full control (create, toggle, delete)    | Enable feature for all users       |
| **Developer**    | Toggle flags, view audit logs            | Enable feature for beta testers    |
| **Product Manager** | Update targeting rules, not rollouts   | Adjust feature for specific region |
| **End User**     | No flag control, only feature access     | Access feature if flag is ON       |

---

## 🚀 Steps to Enable/Disable Feature Flags

### 1. Define Roles and Permissions
- Identify roles in your PepsiCo team (e.g., Admin, Dev, PM).
- Assign permissions in the feature flag tool:
  ```bash
  # Example: Flagsmith CLI to create a role
  flagsmith create-role --name "Developer" --permissions "toggle_flag, view_logs"
  ```
- Map roles to user groups in SSO (e.g., Okta, Azure AD).

### 2. Create a Feature Flag
- Use the tool’s UI or API to create a flag.
  ```javascript
  // Example: LaunchDarkly API
  {
    "key": "pepsico_new_dashboard",
    "name": "New Dashboard Feature",
    "description": "Enables PepsiCo's revamped dashboard"
  }
  ```

### 3. Enable Flag for Specific Role
- Configure the flag to check user role.
  ```javascript
  // Pseudo-code (Custom system)
  if (user.role === "Admin" || user.role === "Developer") {
    featureFlag("pepsico_new_dashboard").enable();
  }
  ```
- Example in Flagsmith:
  1. Go to **Features** > Select `pepsico_new_dashboard`.
  2. Set **Targeting** to role-based group (e.g., `front_end_devs`).
  3. Toggle ON for the group.

### 4. Disable Flag for Role
- Update targeting rules to exclude roles.
  ```bash
  # Example: Unleash CLI
  unleash toggle --flag pepsico_new_dashboard --environment production --off --role "End User"
  ```
- In LaunchDarkly:
  1. Navigate to **Targeting** tab.
  2. Remove role (e.g., `Product Manager`) from allowed list.
  3. Save changes.

### 5. Audit Changes
- Check audit logs to ensure compliance.
  ```bash
  # Flagsmith: View logs
  flagsmith audit-log --flag pepsico_new_dashboard
  ```
- Example log:
  ```
  2025-06-18 13:21 IST: Admin (john.smith@pepsico.com) enabled pepsico_new_dashboard for Developers.
  ```

---

## 🧑‍💻 Code Example (C# with Microsoft.FeatureManagement)
For .NET-based PepsiCo apps:
```csharp
public class FeatureFlagService
{
    private readonly IFeatureManager _featureManager;

    public FeatureFlagService(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }

    public async Task<bool> IsFeatureEnabled(string feature, string userRole)
    {
        // Check if feature is enabled for role
        if (userRole == "Admin" || userRole == "Developer")
        {
            return await _featureManager.IsEnabledAsync(feature);
        }
        return false;
    }
}

// Usage
var isEnabled = await featureFlagService.IsFeatureEnabled("pepsico_new_dashboard", currentUser.Role);
if (isEnabled) {
    // Show feature
}
```

---

## 🔐 Best Practices
1. **RBAC Integration**: Sync roles with SSO for consistency.[](https://configu.com/blog/feature-flags-management-key-features-5-powerful-solutions/)
2. **Audit Logs**: Track who toggled flags for compliance.[](https://docs.getunleash.io/topics/feature-flags/best-practices-using-feature-flags-at-scale)
3. **Flag Cleanup**: Remove obsolete flags to avoid tech debt.[](https://launchdarkly.com/blog/what-are-feature-flags/)
4. **Testing**: Use beta groups (e.g., `pepsico_beta_testers`) before full rollout.
5. **Documentation**: Maintain a flag registry (purpose, status, owner).

---

## ⚠️ Common Pitfalls
- **Over-Privileged Roles**: Avoid giving End Users toggle access.
- **Stale Flags**: Schedule flag removal after rollout.
- **No Audit**: Always enable logging for traceability.

---

## 📚 References
- [Flagsmith RBAC Guide](https://docs.flagsmith.com)[](https://www.freecodecamp.org/news/feature-flags-and-role-based-access-control-devops/)
- [LaunchDarkly Feature Flags](https://launchdarkly.com)[](https://launchdarkly.com/blog/what-are-feature-flags/)
- [Unleash Best Practices](https://docs.getunleash.io)[](https://docs.getunleash.io/topics/feature-flags/best-practices-using-feature-flags-at-scale)
- [Microsoft Feature Management](https://learn.microsoft.com)[](https://learn.microsoft.com/en-us/azure/azure-app-configuration/feature-management-dotnet-reference)


