# Managed Bean

Rules for JSF managed beans in Axon Ivy HTML Dialogs. **Prefer using a managed bean over Axon Ivy process logic** for UI-related behavior (validation, dynamic visibility, uploads, autocomplete). Keep process logic (`#{logic.xxx}`) only for navigation (submit/cancel).

**Key principle: Beans NEVER reference `#{data.xxx}` or `#{logic.xxx}`. The XHTML handles ALL data bridging.**

## When to Create a Bean

Create a managed bean when a dialog needs:
- Validation or business logic beyond simple field binding
- Dynamic UI visibility/read-only control
- File upload/download handling
- Autocomplete / dynamic dropdown logic
- Any reusable UI behavior

## File Location

Beans go in `src/package/managedbean/` (or `src/package/bean/`). **NEVER** in `src_hd/`.

## Class Structure

```java
package package.managedbean;

import java.io.Serializable;
import javax.faces.bean.ManagedBean;
import javax.faces.bean.ViewScoped;

@ManagedBean
@ViewScoped
public class MyBean implements Serializable {

  private static final long serialVersionUID = 1L;

  private MyEntity entity;
  private boolean readOnly;

  /**
   * Called by XHTML:
   *   <f:event listener="#{myBean.preRender(data.entity, data.isReadOnly)}" type="preRenderComponent" />
   */
  public void preRender(MyEntity entity, Boolean readOnly) {
    this.entity = entity != null ? entity : new MyEntity();
    this.readOnly = Boolean.TRUE.equals(readOnly);
  }

  public void save() {
    entity = MyService.save(entity);
  }

  public MyEntity getEntity() { return entity; }
  public void setEntity(MyEntity entity) { this.entity = entity; }

  public boolean isReadOnly() { return readOnly; }
  public void setReadOnly(boolean readOnly) { this.readOnly = readOnly; }
}
```

## Rules

1. **Always `implements Serializable`** — `@ViewScoped` beans must be serializable.
2. **Always `@ManagedBean @ViewScoped`** — one bean instance per page view.
3. **Use `preRender()` for init** — never use constructor for Ivy API calls.
4. **Bean NEVER references `#{data.xxx}` or `#{logic.xxx}`** — XHTML handles bridging.
5. **All bridged fields need getter AND setter** — `setPropertyActionListener` calls setters.
6. **Prefer bean methods over `#{logic.xxx}`** for business logic — use `#{logic.xxx}` only for navigation.

## Data Bridging (XHTML Side)

### Init: Process Data → Bean

```xml
<f:event listener="#{myBean.preRender(data.entity, data.isReadOnly)}" type="preRenderComponent" />
```

### Submit: Bean → Process Data + Navigate

```xml
<!-- Simple: setPropertyActionListener pushes data, then logic navigates -->
<p:commandButton value="Save" actionListener="#{logic.save}" process="@form" update="form">
  <f:setPropertyActionListener target="#{data.entity}" value="#{myBean.entity}" />
</p:commandButton>

<!-- Two-step: bean processes first, then remoteCommand pushes data + navigates -->
<p:commandButton value="Submit" actionListener="#{myBean.save()}"
                 process="@form" update="form"
                 oncomplete="if(!args.validationFailed) submitToProcess();" />
<p:remoteCommand name="submitToProcess" actionListener="#{logic.save}" process="@this">
  <f:setPropertyActionListener target="#{data.entity}" value="#{myBean.entity}" />
</p:remoteCommand>
```

### Cancel: Direct Logic Call

```xml
<p:commandLink value="Cancel" actionListener="#{logic.close}" process="@this" immediate="true" />
```

## File Upload Listener

The bean method must accept `FileUploadEvent` (not no-arg):

```java
import org.primefaces.event.FileUploadEvent;

public void upload(FileUploadEvent event) {
  if (event == null || event.getFile() == null) return;
  try {
    java.io.File tempFile = java.io.File.createTempFile("upload_", ".pdf");
    java.nio.file.Files.write(tempFile.toPath(), event.getFile().getContent());
    this.inputFile = tempFile;
  } catch (Exception e) {
    Ivy.log().error("Failed to process uploaded file", e);
  }
}

public void removeFile() {
  if (inputFile != null && inputFile.exists()) inputFile.delete();
  inputFile = null;
}
```

## Displaying Messages

```java
FacesContext.getCurrentInstance().addMessage("form-messages",
    new FacesMessage(FacesMessage.SEVERITY_ERROR, "", "Validation failed"));
```

## Example: RoleSelectionBean (Autocomplete Component)

A bean that provides autocomplete logic for a reusable composite component.

**Bean:** `src/com/axonivy/portal/components/bean/RoleSelectionBean.java`

```java
package com.axonivy.portal.components.bean;

import java.io.Serializable;
import java.util.List;
import javax.annotation.PostConstruct;
import javax.el.MethodExpression;
import javax.faces.bean.ManagedBean;
import javax.faces.bean.ViewScoped;

@ManagedBean
@ViewScoped
public class RoleSelectionBean implements Serializable {

  private static final long serialVersionUID = 1L;
  private MethodExpression completeRoleMethod;

  @PostConstruct
  public void init() {
    completeRoleMethod = BeanUtils.createCompleteMethod("#{roleSelectionBean.completeRole}");
  }

  public List<RoleDTO> completeRole(String query) {
    List<String> fromRoles = Attrs.currentContext().getAttribute("#{cc.attrs.fromRoleNames}", List.class);
    List<String> excludedRoleNames = Attrs.currentContext().getAttribute("#{cc.attrs.excludedRoleNames}", List.class);
    return RoleUtils.findRoles(fromRoles, excludedRoleNames, query);
  }

  public MethodExpression getCompleteRoleMethod() {
    return completeRoleMethod;
  }
}
```

**XHTML (composite component):** `src_hd/.../RoleSelection.xhtml`

```xml
<cc:interface componentType="IvyComponent">
  <cc:attribute name="selectedRole" type="com.axonivy.portal.components.dto.RoleDTO" required="true" />
  <cc:attribute name="completeMethod" method-signature="java.util.List completeMethod(java.lang.String)" />
  <!-- ... other attributes ... -->
</cc:interface>

<cc:implementation>
  <c:set var="completeMethod"
    value="#{not empty cc.attrs.completeMethod ? cc.attrs.completeMethod : roleSelectionBean.completeRoleMethod}" />

  <p:autoComplete id="#{cc.attrs.componentId}"
    value="#{cc.attrs.selectedRole}"
    completeMethod="#{completeMethod}"
    var="role" itemValue="#{role}" itemLabel="#{role.displayName}"
    forceSelection="true" dropdown="true" converter="pojoConverter" />
</cc:implementation>
```

**Key patterns shown:**
- Bean provides a `MethodExpression` for `p:autoComplete completeMethod`
- XHTML falls back to bean method when no custom `completeMethod` attribute is passed
- Bean reads composite component attributes via `Attrs.currentContext().getAttribute()`

## Common Mistakes

- **Bean in `src_hd/`** — always use `src/package/managedbean/` or `src/package/bean/`
- **Constructor with Ivy API** — use `preRender()` or `@PostConstruct` instead
- **Missing setter** for fields used in `setPropertyActionListener` — silently fails
- **No-arg upload method** with `listener` attribute — must accept `FileUploadEvent`
