# Code Quality Rules — M4, L1–L3

Apply to all files.

---

**M4. Self-Documenting Code — No Restatement Comments**
*Scan for:* inline comments that restate what the code already clearly expresses; sub-steps inside a method described by a comment instead of a named private method.
*Why:* Restatement comments become outdated and misleading. If a comment describes a step, extract that step into a private method whose name *is* the documentation. Reserve comments only for non-obvious *why* reasoning.

---

**L1. Dedicated Types for Constants and Error Codes**
*Scan for:* error codes, status strings, or configuration keys scattered as fields on large classes rather than in a dedicated interface or enum.
*Why:* Scattered constants are hard to discover and maintain. Extract into a dedicated type (e.g., `GuardrailErrors`, `StatusCodes`).

---

**L2. YAGNI — No Speculative Code**
*Scan for:* utility classes, helper methods, or configuration hooks with no current caller; abstractions introduced for hypothetical future requirements; filter fields wired to UI forms but never passed to any query.
*Why:* Code written for futures that may never arrive adds maintenance burden with zero present value. Delete it; re-add when a concrete use case exists.

---

**L3. Unit Test Coverage for Complex Logic**
*Scan for:* new algorithmic units (parsers, converters, validators, correlators, tree builders) with no accompanying test class.
*Why:* Non-trivial logic without tests is a maintenance liability. Testability pressure also often reveals missing interface extraction — a design benefit, not just a testing convenience.
