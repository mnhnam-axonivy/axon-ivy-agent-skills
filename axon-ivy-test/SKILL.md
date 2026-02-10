---
name: axon-ivy-test
description: Entry point for creating and update tests. Use this skill when user asks to write/update/delete tests.
---

## Step 1: Check Project Setup

**Check if `src_test/` folder exists and `pom.xml` has test dependency.**

Look for in `pom.xml`:

```xml
<dependency>
  <groupId>com.axonivy.ivy.api.test</groupId>
  <artifactId>ivy-test-api</artifactId>
  <scope>test</scope>
</dependency>
```

- **Not found** → Load `setup.md` to add testing infrastructure
- **Found** → Proceed to Step 2

## Step 2: Auto-Detect Test Type

### By File Type

- `processes/**/*.p.json` → Process Test → Load `process.md`
- `src_hd/**/*Process.p.json` → Process Test → Load `process.md`
- `*.java` → Read file content → See Step 2b

### By Java File Content (Step 2b)

Read the target Java file and check for annotations:

- `@Path`, `@GET`, `@POST`, `@PUT`, `@DELETE`, `@Produces`, `@Consumes` → REST Test → Load `rest.md`
- None of above → Unit Test → Load `unit.md`

**Detection code patterns:**

```java
// REST endpoint indicators
@Path("...")
@GET @POST @PUT @DELETE @PATCH
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
javax.ws.rs.*

// Unit test targets (no REST annotations)
public class MyService { ... }
public class MyRepository { ... }
public class MyUtils { ... }
```

## Step 3: Additional References

Load these references when needed:

- Set Ivy variables for test → Load `fixture.md`
- Mock external REST API → Load `mock.md`
- Load JSON fixtures → Load `resource.md`
- Assert log messages → Load `log.md`
- AssertJ patterns → Load `assert.md`

## Test File Location

Place test in `src_test/` mirroring the source structure:

- `src/com/example/MyService.java` → `src_test/com/example/TestMyService.java`
- `processes/myflow/Flow.p.json` → `src_test/myflow/TestFlow.java`

## Checklist

- [ ] `src_test/` folder exists
- [ ] `pom.xml` has `ivy-test-api` dependency
- [ ] Test class has correct annotation
- [ ] Tests run with `mvn test`
