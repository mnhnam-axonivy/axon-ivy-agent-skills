---
name: axon-ivy-variable-config
description: Provide information and rules for Axon Ivy variables configurations. Use when wokring with Axon Ivy variable.
---

## Configuration Files

`config/variables.yaml` : Environment variables

## variables.yaml

```yaml
Variables:
  # String (default type)
  AppName: "My Application"

  # Integer
  MaxRetries:
    # [type: Integer]
    value: 3

  # Password (encrypted, hidden in UI)
  Api:
    Secret:
      # [type: Password]
      value: "secret-value"

  # JSON file reference
  Portal:
    # [file: json]
    Dashboard: ""

    # Nested JSON in subfolder
    Processes:
      # [file: json]
      ExternalLinks: ""

  # Environment-specific (use [default] for fallback)
  Database:
    Host:
      [default]: "localhost"
      [production]: "db.example.com"
    Port: 5432
```

## JSON Variable Folder Structure

For JSON-type variables, create a `variables` folder under `config/`:

```text
config/
├── variables.yaml
└── variables/
    └── Portal/                    # Matches "Portal:" section
        ├── Dashboard.json         # Portal.Dashboard
        └── Processes/             # Nested namespace
            └── ExternalLinks.json # Portal.Processes.ExternalLinks
```

### JSON File Examples

**Empty array** (`Portal/Processes/ExternalLinks.json`):

```json
[]
```

**Complex configuration** (`Portal/Dashboard.json`):

```json
[
  {
    "id": "default-dashboard",
    "version": "13.0.0",
    "titles": [
      { "locale": "en", "value": "Dashboard" }
    ],
    "widgets": []
  }
]
```

## Variable Access in Code

```java
// Access variables in Java/IvyScript
String appName = Ivy.var().get("AppName");
String apiUrl = Ivy.var().get("Api.BaseUrl");

// In EL expressions
#{ivy.var.AppName}
#{ivy.var.Api.BaseUrl}
```
