---
name: axon-ivy-java-data
description: Rules and patterns for Java model classes, enums, DTOs, and persistence (Ivy.repo() or JPA/SQL) in Axon Ivy projects.
---

## Use Together With

- `axon-ivy-repository` - For persistence with Ivy.repo()

## File Locations

| Type | Location |
|------|----------|
| Model Class | `src/package/model/` |
| Enum | `src/package/model/` |
| DTO | `src/package/dto/` |

## Java Model Pattern

**File:** `src/package/model/Entity.java`

```java
package package.model;

import java.util.UUID;

public class Entity {

  private String id;
  private String name;
  private EntityStatus status;

  public Entity() {
    this.id = UUID.randomUUID().toString();
    this.status = EntityStatus.NEW;
  }

  // Getters and Setters
  public String getId() { return id; }
  public void setId(String id) { this.id = id; }

  public String getName() { return name; }
  public void setName(String name) { this.name = name; }

  public EntityStatus getStatus() { return status; }
  public void setStatus(EntityStatus status) { this.status = status; }

  @Override
  public String toString() {
    return "Entity [id=" + id + ", name=" + name + "]";
  }
}
```

## Enum Pattern

**File:** `src/package/model/EntityStatus.java`

```java
package package.model;

public enum EntityStatus {
  NEW("New"),
  IN_PROGRESS("In Progress"),
  COMPLETED("Completed"),
  CANCELLED("Cancelled");

  private final String description;

  EntityStatus(String description) {
    this.description = description;
  }

  public String getDescription() {
    return description;
  }

  public boolean isCompleted() {
    return this == COMPLETED;
  }

  public boolean isCancelled() {
    return this == CANCELLED;
  }

  public boolean isActive() {
    return this != COMPLETED && this != CANCELLED;
  }
}
```

## Model with Builder Pattern

```java
public class Project {

  private String projectId;
  private String name;
  private String description;

  public Project() {
    this.projectId = UUID.randomUUID().toString();
  }

  // Fluent builder methods
  public Project name(String name) {
    this.name = name;
    return this;
  }

  public Project description(String description) {
    this.description = description;
    return this;
  }

  // Standard getters/setters...
}
```

## Model with AI Description

ONLY use if this project depend on the `smart-workflow` project or LangChain4j by checking the `pom.xml`.

For models used with AI/LLM processing:

```java
package package.model;

import dev.langchain4j.model.output.structured.Description;

@Description("Brief description for AI context")
public class Candidate {

  @Description("Unique identifier")
  private String id;

  @Description("Full name of the candidate")
  private String name;

  @Description("List of technical skills")
  private List<String> skills;
}
```
