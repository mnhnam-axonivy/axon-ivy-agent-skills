---
name: axon-ivy-user-role-config
description: Provide format for Axon Ivy users/roles configurations. Use when wokring with Axon Ivy users/roles.
---

## Configuration Files

`config/roles.yaml` : Role hierarchy and permissions
`config/users.yaml` : User definitions

## roles.yaml

```yaml
Roles:
  # Parent roles
  Everybody:
  HR:
    parent: Everybody
  Manager:
    parent: Everybody

  # Child roles
  Recruiter:
    parent: HR
  ProjectManager:
    parent: Manager
```

## users.yaml

```yaml
Users:
  pm_user:
    fullName: Project Manager
    password: pm_user
    email: pm@example.com
    roles:
      - HR
      - ProjectManager
```
