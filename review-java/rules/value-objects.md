# Value Object Rules — H2, M6

Apply when the file is a simple data class (fields + getters/setters), has an inner class that holds data, or uses `extends` for code reuse.

---

**H2. Composition over Inheritance**
*Scan for:* `extends` used for code reuse (not domain classification); classes that inherit shared constants or utility methods from a base class; parallel subclasses that share duplicated logic via an abstract superclass.
*Why:* Inheritance for reuse creates rigid coupling. A change in the base class silently breaks all subclasses. Use `implements` + delegation: extract the shared logic into a helper, compose via a field.
```java
// Before: two classes extend base to share formatting logic
class CaseHistoryGroup extends HistoryGroupBase { ... }
class TaskHistoryGroup extends HistoryGroupBase { ... }

// After: both implement interface, compose shared logic via a field
class CaseHistoryGroup implements HistoryGroupView {
  private final HistoryGroupView stats = HistoryGroupView.of(entries);
  @Override public int getMessageCount() { return stats.getMessageCount(); }
  ...
}
```

---

**M6. Nested Record for Owned Value Objects**
*Scan for:* simple value-holder classes (no business logic, no setters called outside their owner) that are used exclusively by one parent class and live in the same package as that parent.
*Why:* A class used only by one owner adds a file with no benefit. A nested `public record` eliminates the file, enforces immutability, and provides correct `equals()`/`hashCode()` for free — which fixes silent test failures when comparing objects after JSON round-trip (`containsExactly` falls back to reference equality without `equals()`).

Two caveats when converting to a record:
1. **Jackson deserialization** — add `@JsonProperty` on each record component when using `ObjectMapper` without `ParameterNamesModule` (otherwise deserialization silently produces nulls).
2. **Accessor naming** — record accessors are `field()` not `getField()`; update every call site.
```java
// Before: standalone class — separate file, no equals(), getField() accessors
public class ToolExecution {
  private String toolName;
  public String getToolName() { return toolName; }
  ...
}

// After: nested record in the owning entity
public class AgentConversationEntry {
  public record ToolExecution(
      @JsonProperty("toolName")   String toolName,
      @JsonProperty("arguments")  String arguments,
      @JsonProperty("resultText") String resultText,
      @JsonProperty("executedAt") String executedAt) {}
  ...
}
// Callers: import com.example.AgentConversationEntry.ToolExecution;
// Accessors: entry.toolName()  (not entry.getToolName())
```
