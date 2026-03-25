---
name: axon-ivy-primefaces-verify
description: Verification checklist for PrimeFaces components — column widths, table layout, AJAX updates, and common rendering pitfalls.
---

**MANDATORY**: Run this checklist after creating or modifying any PrimeFaces `p:dataTable`, `p:treeTable`, or `p:column` component. Read the XHTML and CSS, then verify each check below. Fix any violations before considering the task done.

## Checklist

### 1. Column Width — Use `width` attribute with percentages, not CSS fixed pixels

Set column widths via the `width` attribute on `p:column` using percentages. Do NOT use CSS `width: Npx` on column style classes.

```xhtml
<!-- WRONG -->
<p:column headerText="Name" styleClass="col-name">  <!-- relies on .col-name { width: 280px } -->

<!-- RIGHT -->
<p:column headerText="Name" width="35%">
```

Action columns (buttons only) should have no `width` attribute — use `white-space: nowrap` in CSS to shrink to content.

---

### 2. Column Width — Size based on intended content

Choose percentages based on what the column displays:

| Content Type | Typical Width |
|---|---|
| Name / identifier / description (case name, task name, UUID) | 30–40% |
| Date / timestamp | 12–15% |
| Short numeric / badge (count, tokens) | 7–10% |
| Model name / enum | 12–18% |
| Action button(s) | fit-content (no `width`) |

The total of all explicit widths should stay under 100%.

---

### 3. AJAX Update Targets — Use `<h:panelGroup id>`, not `<div id>`

When using `update="someId"` on a command button or AJAX listener, the target must be a JSF component with an `id`. Plain HTML `<div id>` elements are not in the JSF component tree and will silently fail to re-render.

```xhtml
<!-- WRONG — plain div, not updatable by AJAX -->
<div id="table-section">...</div>

<!-- RIGHT — JSF component, fully updatable -->
<h:panelGroup id="table-section" layout="block">...</h:panelGroup>
```

---

### 4. Component IDs — Use hyphen-case

All `id` attributes on JSF/PrimeFaces components must use hyphen-case (kebab-case).

```xhtml
<!-- WRONG -->
<h:panelGroup id="tableSection" ...>
<h:form id="mainForm" ...>

<!-- RIGHT -->
<h:panelGroup id="table-section" ...>
<h:form id="main-form" ...>
```
