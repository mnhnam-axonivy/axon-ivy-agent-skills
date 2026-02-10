# Unit Testing

Unit testing with `@IvyTest` for business logic without workflow execution.

## File Location

`src_test/package/Test*.java`

## Basic Pattern

```java
package package.test;

import static org.assertj.core.api.Assertions.assertThat;
import org.junit.jupiter.api.Test;
import ch.ivyteam.ivy.environment.IvyTest;

@IvyTest
public class TestMyService {

  @Test
  void testMethod() {
    var result = MyService.calculate(10);
    assertThat(result).isEqualTo(20);
  }
}
```

## With AppFixture

```java
@IvyTest
public class TestWithConfig {

  @BeforeEach
  void setup(AppFixture fixture) {
    fixture.var("myVar", "testValue");
  }

  @Test
  void testWithVariable() {
    assertThat(Ivy.var().get("myVar")).isEqualTo("testValue");
  }
}
```
