---
name: axon-ivy-process-verify
description: Verification checklist for Axon Ivy process files (.p.json). MUST be used after axon-ivy-process skill to catch common errors.
---

**MANDATORY**: Run this checklist on EVERY `.p.json` file after creating or modifying it with the `axon-ivy-process` skill. Read the generated file, then verify each applicable check below. Fix any violations before considering the task done.

## Checklist

### 1. SubProcessCall — Path uses `/` (slash), NOT `.` (dot)

Scan every `SubProcessCall` element. The `processCall` value must use `/` as path separator.

```
WRONG: "processCall": "hr.onboarding.agent.ExtractEmployeeData:extractEmployeeData(String)"
RIGHT: "processCall": "hr/onboarding/agent/ExtractEmployeeData:extractEmployeeData(String)"
```

Note: `DialogCall.dialog` uses `.` (dot) — the opposite convention. Do not mix them up.

### 2. DialogCall — Path uses `.` (dot), NOT `/` (slash)

Scan every `DialogCall` element. The `dialog` value must use `.` as package separator.

```
WRONG: "dialog": "package/DialogName:start()"
RIGHT: "dialog": "package.DialogName:start()"
```

### 3. Expiry timeout — Must use `new Duration(...)` constructor

Scan every `TaskSwitchEvent` that has an `expiry.timeout`. The value must be a `Duration` constructor, not a shorthand string.

```
WRONG: "timeout": "3d"
WRONG: "timeout": "3D"
WRONG: "timeout": "5h"
RIGHT: "timeout": "new Duration(\"3D\")"
RIGHT: "timeout": "new Duration(\"5H\")"
```

Rule: Capital `D` for days, capital `H` for hours, wrapped in `new Duration("...")`.

### 4. HtmlDialogStart — Must have `result` section if returning data

Scan every `HtmlDialogStart` element. If the calling process reads `result.*` in its output mapping, the dialog's `HtmlDialogStart` MUST declare a `result` section.

```json
// REQUIRED when dialog returns data:
"result": {
  "params": [
    { "name": "project", "type": "package.model.Project", "desc": "" }
  ],
  "map": {
    "result.project": "in.project"
  }
}
```

Missing `result` causes: `Output 'out.field': Field fieldName not found for class <>`.

### 5. Alternative — Every connection must have a matching condition entry

Scan every `Alternative` element. Count the entries in `connect` and the keys in `conditions`. They MUST match.

```
WRONG (3 connections, 2 conditions):
  "conditions": { "c1": "expr1", "c2": "expr2" }
  "connect": [{ "id": "c1" }, { "id": "c2" }, { "id": "c3" }]

RIGHT (3 connections, 3 conditions — default uses ""):
  "conditions": { "c1": "expr1", "c2": "expr2", "c3": "" }
  "connect": [{ "id": "c1" }, { "id": "c2" }, { "id": "c3" }]
```

Rule: Default/fallback branch condition value is `""` (empty string).

### 6. Script elements — No `param.*` usage

Scan every `Script` element's `output.code`. The variable `param` is NOT available in scripts — use `in` instead.

```
WRONG: String name = param.employeeName;
RIGHT: String name = in.employeeName;
```

`param.*` is only available in start element signatures (`RequestStart`, `CallSubStart`, `HtmlDialogStart`).

### 7. Element ID / Connection ID — No duplicates across both

Collect ALL element `id` values and ALL connection `id` values in the file. They share one namespace and MUST NOT collide.

```
WRONG: element id "f5" AND connection id "f5"
RIGHT: elements f0–f5, connections f6–f10
```
