## 2.5.15. Soft Assertions

With soft assertions AssertJ collects all assertion errors instead of stopping at the first one.

> soft assertions AssertJ를 사용하면 하나의 검증이 틀렸을 때 멈추지 않고 모든 검증 로직을 실행합니다.

> This is especially useful for long tests like end-to-end tests
as we can fix all reported errors at once and avoid multiple failing runs.

> 이것은 여러 번의 실패를 반복하지 않고 모든 오류를 한번에 수정할 수 있으므로 end-to-end 테스트와 같은 긴 테스트에 특히 유용합니다.

Since soft assertions don’t fail at the first error,
you need to tell AssertJ when to report the captured assertion errors,
there are different ways of doing so:

> soft assertions은 첫 번째 실패에 멈추지 않기 때문에, 잡아낸 assertion 오류를 보고할 시기를 AssertJ에게 알려야합니다. 이를 설정하는 방법은 여러가지가 있습니다:

- Calling `assertAll()` (basic approach)
- Using a JUnit 4 rule that takes care of calling `assertAll()` after each tests
- Using the provided JUnit 5 extension which injects a `SoftAssertions` or a `BDDSoftAssertions` parameter and calls `assertAll()` after each tests
- Using a `AutoCloseableSoftAssertions`
- Using `assertSoftly` static method

> - `assertAll()`을 호출하는 것 (기본적인 접근 방법)
> - 각 테스트 진행 후 `assertAll()`을 호출하는 Junit4의 규칙 사용
> - `SoftAssertions` 또는 `BDDSoftAssertions`를 매개변수로 주입하고 각 테스트 후에 assertAll()을 호출하는 Junit5에서 제공하는 확장을 사용
> - `AutoCloseableSoftAssertions`을 사용하는 방법
> - `assertSoftly` 정적 메서드를 사용하는 방법

Soft assertions comes with a BDD flavor where `assertThat` is replaced by `then`.

> Soft assertions은 `assertThat`이 `then`으로 대체되는 BDD 스타일도 함께 제공합니다.

If you have created your own custom Soft assertions
it is possible to combine them all in a single soft assertions entry point.

> 만약 사용자 정의 Soft assertions을 만들었다면 단일 검증의 진입 포인트에서 모두 결합할 수 있습니다.

Let’s see first how to use soft assertions requiring an explicit call to `assertAll()`,
the other approaches that don’t require this explicit call are described in the subsequent sections.

> 먼저 `assertAll()`에 대한 명시적인 호출이 필요한 soft assertion을 사용하는 방법을 살펴보겠습니다.
> 이러한 명시적 호출이 필요없는 다른 방법은 이어지는 다음 파트에서 설명합니다.

Example:

``` java
@Test
void basic_soft_assertions_example() {
    SoftAssertions softly = new SoftAssertions(); // soft assertion 인스턴스 생성

    softly.assertThat("George Martin").as("great authors").isEqualTo("JRR Tolkien"); // 검증 추가 1
    softly.assertThat(42).as("response to Everything").isGreaterThan(100); // 검증 추가 2
    softly.assertThat("Gandalf").isEqualTo("Sauron"); // 검증 추가 3

    // Don't forget to call assertAll() otherwise no assertion errors are reported!
    // assertAll()을 호출하여 결과를 보고 받는 것을 잊지마세요!
    softly.assertAll(); // assertAll 호출
}
```

1. Build a `SoftAssertions` instance to record all assertion errors
2. Use `softly.assertThat` instead of the usual `assertThat` methods
3. Don’t forget to call `assertAll()` to report all assertion errors!

The previous test fails with the message below reporting all the errors:

> 위의 테스트는 모든 오류를 보고하고 실패합니다.

```
Multiple Failures (3 failures)
-- failure 1 --
[great authors]
Expecting:
    <"George Martin">
to be equal to:
    <"JRR Tolkien">
but was not.
-- failure 2 --
[response to Everything]
Expecting:
<42>
to be greater than:
    <100>
-- failure 3 --
Expecting:
    <"gandalf">
to be equal to:
    <"sauron">
but was not.
```

### BDD Soft assertions

> BDD는 "Behavior-Driven Development"의 약어로, 행위 주도 개발을 의미합니다.

BDD aficionados can use BDD soft assertions where `assertThat` is replaced by `then`.

> BDD 스타일을 좋아한다면 `assertThat` 대신 `then`을 사용할 수 있습니다.

Example:

``` java
@Test
void basic_bdd_soft_assertions_example() {
    BDDSoftAssertions softly = new BDDSoftAssertions(); // BDDSoftAssertion 인스턴스 생성

    softly.then("George Martin").as("great authors").isEqualTo("JRR Tolkien"); // `assertThat` 대신 `then` 사용
    softly.then(42).as("response to Everything").isGreaterThan(100);
    softly.then("Gandalf").isEqualTo("Sauron");

    // Don't forget to call assertAll() otherwise no assertion errors are reported!
    // 마찬가지로 assertAll()을 호출하는 것을 잊지마세요!
    softly.assertAll();
}
```

There are BDD soft assertions versions for the different soft assertions approaches:

> 다양한 soft assertion에 따른 다양한 BDD 스타일의 soft assertion도 존재합니다:

- `AutoCloseableBDDSoftAssertions`
- Using `JUnitBDDSoftAssertions` that takes care of calling `assertAll()` after each tests
- Using a JUnit 5 extension that takes care of calling `assertAll()` after each tests

> - `AutoCloseableBDDSoftAssertions`
> - 각 테스트 후 `assertAll()`을 호출하는 `JUnitBDDSoftAssertions` 사용
> - 각 테스트 후 `assertAll()` 호출을 처리하는 Junit5의 확장 사용

<br/>

### JUnit 4 Soft assertions rule

The JUnit rule provided by AssertJ takes care of calling `assertAll()` at the end of each test.

> AssertJ에서 제공하는 Junit rule은 각 테스트가 끝날 때 `assertAll()`을 호출하여 처리합니다.

Example:

``` java
@Rule // Rule 애너테이션 사용
public final JUnitSoftAssertions softly = new JUnitSoftAssertions();

@Test
void junit4_soft_assertions_example() {
    softly.assertThat("George Martin").as("great authors").isEqualTo("JRR Tolkien");  
    softly.assertThat(42).as("response to Everything").isGreaterThan(100);
    softly.assertThat("Gandalf").isEqualTo("Sauron");
    // No need to call softly.assertAll(), this is automatically done by the JUnitSoftAssertions rule
    // JUnitSoftAssertions rule에 의해 자동으로 처리되기 때문에 assertAll을 호출할 필요가 없습니다.
}
```

In a similar way you can use `JUnitBDDSoftAssertions` where `assertThat` is replaced by `then`:

> 유사한 방식으로 `assertThat`을 `then`으로 대체할 수 있는 `JUnitBDDSoftAssertions`을 사용할 수도 있습니다.

``` java
@Rule // Rule 애너테이션 사용
public final JUnitBDDSoftAssertions softly = new JUnitBDDSoftAssertions();

@Test
void junit4_bdd_soft_assertions_example() {
    softly.then("George Martin").as("great authors").isEqualTo("JRR Tolkien");
    softly.then(42).as("response to Everything").isGreaterThan(100);
    softly.then("Gandalf").isEqualTo("Dauron");
    // No need to call softly.assertAll(), this is automatically done by the JUnitSoftAssertions rule
    // JUnitSoftAssertions rule에 의해 자동으로 처리되기 때문에 assertAll을 호출할 필요가 없습니다.
}
```

<br/>

### JUnit 5 soft assertions extension

`SoftAssertionsExtension` is a JUnit 5 extension that:

> `SoftAssertionsExtension`은 Junit 5의 확장으로:

- takes care of calling `assertAll()` at the end of each tests
- supports initializing `SoftAssertionsProvider` field annotated with `@InjectSoftAssertions`
- supports injecting a `SoftAssertionsProvider` parameter in each test methods

> - 각 테스트가 끝날 때 자동으로 `assertAll()`을 호출하여 처리합니다.
> - `@InjectSoftAssertions`을 사용하여 `SoftAssertionsProvider`의 필드를 초기화할 수 있습니다.
> - 각 테스트 메서드에 `SoftAssertionsProvider` 매개변수 주입을 지원합니다.

`SoftAssertionsProvider` is the interface that any concrete soft assertions class must implement,
AssertJ provides two of them: `SoftAssertions` and `BDDSoftAssertions`,
but custom implementations are also supported as long as they have a default constructor.
See the end of combining soft assertions entry points section for an example.

> `SoftAssertionsProvider`은 구체적인 soft assertion 클래스가 구현해야 하는 인터페이스입니다.
> AssertJ는 다음 두 가지를 제공합니다: `SoftAssertions`과 `BDDSoftAssertions`
> 그러나 사용자 정의 구현도 기본 생성자가 있다면 지원 가능합니다.
> 예제는 combining soft assertions entry points 파트의 끝 부분을 참고하세요.

> `JUnitJupiterSoftAssertions`, `JUnitJupiterBDDSoftAssertions` and `SoftlyExtension`
> are now deprecated in favor of `SoftAssertionsExtension`.

> `JUnitJupiterSoftAssertions`, `JUnitJupiterBDDSoftAssertions` 그리고 `SoftlyExtension`은
> `SoftAssertionsExtension`을 위해 더 이상 사용하지 않습니다. (deprecated)

**SoftAssertionsProvider field injection**

**SoftAssertionsProvider 필드 주입**

`SoftAssertionsExtension` supports injecting any instance of `SoftAssertionsProvider`
into a class test field annotated with `@InjectSoftAssertions`.
The injection occurs before each test method execution,
after each test `assertAll()` is invoked to verify that no soft assertions failed.

> `SoftAssertionsExtension`은 `SoftAssertionsProvider`의 어떠한 인스턴스든지
> `@InjectSoftAssertions` 애너테이션이 붙은 테스트 클래스의 필드에 주입하는 것을 지원합니다.
> 주입은 각각의 테스트 메서드가 실행되기 전과
> assertAll()이 호출되어 실패한 soft assertion이 없는지 확인하고 발생합니다.

A nested test class can provide a `SoftAssertionsProvider` field
when it extends this extension or can inherit the parent’s one.

> 중첩된 테스트 클래스는 이 확장을 extends 하거나 부모의 확장을 상속할 때 `SoftAssertionsProvider` 필드를 사용할 수 있습니다.

You can have multiple soft assertion providers injected into a single test class.
Assertions made on any of them will be collected in a single error collector and reported all together,
in the same order that they failed.

> 단일 테스트 클래스에 여러 개의 soft assertion provider를 주입할 수 있습니다.
> 하나의 에러 수집기에 함께 집계되고 결과 또한 동일한 순서로 함께 보고됩니다.

This extension throws an `ExtensionConfigurationException` if:

> 이 확장은 다음과 같은 경우 `ExtensionConfigurationException`을 발생시킵니다:

- the field is static or final or cannot be accessed;
- the field type is not a concrete implementation of `SoftAssertionsProvider` (or subclass); or
- the field type has no default constructor.

> - 필드가 static 또는 final이거나 접근할 수 없는 경우
> - 필드 type이 `SoftAssertionsProvider`(또는 하위 클래스)의 구체적인 구현이 아닌 경우
> - 기본 생성자가 없는 경우

Example:

``` java
import org.assertj.core.api.SoftAssertions;
import org.assertj.core.api.junit.jupiter.SoftAssertionsExtension;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

@ExtendWith(SoftAssertionsExtension.class) // Junit5 확장 사용
public class JUnit5SoftAssertionsExtensionAssertionsExamples {

    @InjectSoftAssertions
    private SoftAssertions softly;
    
    @Test
    public void chained_soft_assertions_example() {
        String name = "Michael Jordan - Bulls";
        softly.assertThat(name).startsWith("Mi")
                               .contains("Bulls");
    // no need to call softly.assertAll(), this is done by the extension
    // 확장에 의해서 처리되기 때문에 assertAll()을 호출할 필요가 없습니다.
    }
    
    // nested classes test work too
    // 중첩된 클래스 또한 테스트 가능합니다.
    @Nested
    class NestedExample {
    
        @Test
        public void football_assertions_example() {
            String kylian = "Kylian Mbappé";
            softly.assertThat(kylian).startsWith("Ky")
                                     .contains("bap");
          // no need to call softly.assertAll(), this is done by the extension
          // 확장에 의해서 처리되기 때문에 assertAll()을 호출할 필요가 없습니다.
        }
    }
}
```

**SoftAssertionsProvider parameter injection**

**SoftAssertionsProvider 파라미터 주입**

`SoftAssertionsExtension` supports injecting any `SoftAssertionsProvider` implementation
as a parameter in any test method.

> `SoftAssertionsExtension`은 `SoftAssertionsProvider`의 구현체를 모든 테스트 메서드의 파라미터로 주입하는 것을 지원합니다.

The term "test method" refers to any method annotated
with `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` or `@TestTemplate`.
Notably, the extension is compatible with parameterized tests,
the parameterized arguments must come first and the soft assertions argument last.

> "test method"란, `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory`
> 또는 `@TestTemplate`의 주석이 달린 모든 메서드를 말합니다.
> 특히, 확장은 parameterized test와 호환 가능하며, 파라미터가 먼저 오고 soft assertion 인수가 마지막에 와야합니다.

The scope of the `SoftAssertionsProvider` instance managed by this extension begins
when a parameter of type `SoftAssertionsProvider` is resolved for a test method.
It ends after the test method has been executed, this is when `assertAll()` will be invoked
on the instance to verify that no soft assertions failed.

> 이 확장에 의해 관리되는 `SoftAssertionsProvider` 인스턴스의 범위는 `SoftAssertionsProvider` 유형의 파라미터가 테스트 메서드에 대해 확인될 때 시작됩니다.
> 테스트 메서드가 실행되고 난 이후에 종료됩니다. 이때 인스턴스에서 `assertAll()`이 호출되어 soft assertion이 실패하지 않았는지 확인합니다.

Parameter injection and field injection can be mixed.
Assertions made on the field- and parameter-injected soft assertion providers
will all be collected and reported together when the extension calls `assertAll()`.

> 파라미터 주입과 필드 주입은 섞어서 사용할 수 있습니다.
> 필드 및 파라미터가 주입된 soft assertion provider에 대한 검증은 확장이 `assertAll()`을 호출할 때 모두 수집되어 함께 보고됩니다.

This extension throws a `ParameterResolutionException` if the resolved `SoftAssertionsProvider` :

> 이 확장은 아래의 경우 `ParameterResolutionException`을 발생시킵니다:

- is abstract; or
- has no default constructor.

> - abstract인 경우
> - 기본 생성자가 없는 경우

Example:

``` java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import org.junit.jupiter.api.extension.ExtendWith;
import org.assertj.core.api.BDDSoftAssertions;
import org.assertj.core.api.SoftAssertions;
import org.assertj.core.api.junit.jupiter.SoftAssertionsExtension;

@ExtendWith(SoftAssertionsExtension.class) // Junit5 확장 사용
public class JUnit5SoftAssertionsExample {

    @Test
    void junit5_soft_assertions_multiple_failures_example(SoftAssertions softly) { // SoftAssertions 사용 예시
        softly.assertThat("George Martin").as("great authors").isEqualTo("JRR Tolkien");
        softly.assertThat(42).as("response to Everything").isGreaterThan(100);
        softly.assertThat("Gandalf").isEqualTo("Sauron");
        // No need to call softly.assertAll(), this is automatically done by the SoftAssertionsExtension
        // assertAll()을 호출할 필요가 없습니다. SoftAssertionsExtension에서 자동적으로 처리합니다.
    }
    
    @Test
    void junit5_bdd_soft_assertions_multiple_failures_example(BDDSoftAssertions softly) { // BDDSoftAssertions 사용 예시
        softly.then("George Martin").as("great authors").isEqualTo("JRR Tolkien");
        softly.then(42).as("response to Everything").isGreaterThan(100);
        softly.then("Gandalf").isEqualTo("Sauron");
        // No need to call softly.assertAll(), this is automatically done by the SoftAssertionsExtension
        // assertAll()을 호출할 필요가 없습니다. SoftAssertionsExtension에서 자동적으로 처리합니다.
    }
    
    @ParameterizedTest
    @CsvSource({ "1, 1, 2", "1, 2, 3" })
    // test parameters come first, soft assertion must come last.
    // 테스트 파라미터가 먼저오고 soft assertion이 마지막에 와야 합니다.
    void junit5_soft_assertions_parameterized_test_example(int a, int b, int sum, SoftAssertions softly) { // 파라미터 순서에 주의
        softly.assertThat(a + b).as("sum").isEqualTo(sum);
        softly.assertThat(a).isLessThan(sum);
        softly.assertThat(b).isLessThan(sum);
    }

}
```

<br/>

### Auto Closeable Soft assertions

As `AutoCloseableSoftAssertions` implements `AutoCloseable#close()` by calling `assertAll()`,
when used in a try-with-resources block `assertAll()` is called automatically before exiting the block.

> `AutoCloseableSoftAssertions`는 `assertAll()을 호출하여 ` `AutoCloseable#close()`을 구현하므로,
> try-with-resources 블럭에서 사용될 때, 블럭이 종료되기 전에 `assertAll()`이 자동으로 호출됩니다.

Example:

``` java
@Test
void auto_closeable_soft_assertions_example() {
    try (AutoCloseableSoftAssertions softly = new AutoCloseableSoftAssertions()) { // resource 작성
        softly.assertThat("George Martin").as("great authors").isEqualTo("JRR Tolkien");  
        softly.assertThat(42).as("response to Everything").isGreaterThan(100);
        softly.assertThat("Gandalf").isEqualTo("Sauron");
        // no need to call assertAll, this is done when softly is closed.
        // 블럭이 종료되기 전에 assertAll이 자동으로 호출됩니다.
    }
}
```

In a similar way you can use `AutoCloseableBDDSoftAssertions` where `assertThat` is replaced by `then`:

> 비슷한 방법으로 `AutoCloseableBDDSoftAssertions`을 사용하여 `assertThat` 대신 `then`을 사용할 수 있습니다:

``` java
@Test
void auto_closeable_bdd_soft_assertions_example() {
    try (AutoCloseableBDDSoftAssertions softly = new AutoCloseableBDDSoftAssertions()) { // resource 작성
        softly.then("George Martin").as("great authors").isEqualTo("JRR Tolkien");
        softly.then(42).as("response to Everything").isGreaterThan(100);
        softly.then("Gandalf").isEqualTo("Sauron");
        // no need to call assertAll, this is done when softly is closed.
        // 블럭이 종료되기 전에 assertAll이 자동으로 호출됩니다.
    }
}
```

<br/>

### Soft assertions with assertSoftly

The `assertSoftly` static method takes care of calling `assertAll()` before exiting.

> `assertSoftly` 정적 메서드는 종료 전 `assertAll()`을 호출합니다.

Example:

``` java
@Test
void assertSoftly_example() {
    SoftAssertions.assertSoftly(softly -> {
        softly.assertThat("George Martin").as("great authors").isEqualTo("JRR Tolkien");
        softly.assertThat(42).as("response to Everything").isGreaterThan(100);
        softly.assertThat("Gandalf").isEqualTo("Sauron");
        // no need to call assertAll(), assertSoftly does it for us.
        // assertSoftly 정적 메서드를 사용하면 assertAll()을 명시적으로 호출할 필요가 없습니다.
    });
}
```

<br/>

### Combining soft assertions entry points

Since the 3.16.0 version AssertJ provides a way to combine standard soft assertions
with custom ones in a single entry point.

> AssertJ 3.16.0 버전 이후부터 단일 진입점에서 표준 soft assertion과 사용자 정의 assertion을 결합하는 방법을 제공합니다.

Let’s assume we have written an entry point for `TolkienCharacter` soft assertions
so that we can write assertions like:

> 아래와 같이 작성하기 위해 `TolkienCharacter`의 soft assertion 대한 진입점을 작성했다고 가정해봅시다:

``` java
TolkienSoftAssertions softly = new TolkienSoftAssertions();
softly.assertThat(frodo).hasRace(HOBBIT)
                        .hasName("Frodo");
```

If we want to check standard soft assertions we could make `TolkienSoftAssertions` inherit `SoftAssertions`
but if we want to have `GoTSoftAssertions` too then we are stuck as Java does not allow multiple inheritance.

> 만약 우리가 표준 soft assertion을 확인하길 원한다면 우리는 `TolkienSoftAssertions`가 `SoftAssertions`을 상속하도록 만들 수 있을 것입니다.
> 그러나 Java는 다중 상속을 허용하지 않기 때문에 `GoTSoftAssertions`도 상속받도록 만드는 것은 불가합니다.

The 3.16.0 release introduced the `SoftAssertionsProvider` interface to define soft assertions entry points.

> 3.16.0 릴리즈는 soft assertion 진입점을 정의하기 위한 `SoftAssertionsProvider` 인터페이스를 도입했습니다.

**Step 1**

The first step consists in extending this interface to expose as many custom entry points as you need.
The typical custom `SoftAssertionsProvider` interface exposes default `assertThat` methods, as shown below:

> 첫 번째 단계는 이 인터페이스를 확장하여 필요한 만큼의 사용자 정의 entry point를 만드는 것입니다.
> 일반적인 사용자 정의 `SoftAssertionsProvider` 인터페이스는 아래와 같이 `assertThat` 메서드를 노출합니다.

``` java
public interface TolkienSoftAssertionsProvider extends SoftAssertionsProvider {
    // custom assertions
    // 사용자 정의 검증문
    default TolkienCharacterAssert assertThat(TolkienCharacter actual) {
        return proxy(TolkienCharacterAssert.class, TolkienCharacter.class, actual);
    }
}

// let's add a Game of Thrones entry point
// Game of Thrones entry point를 추가해 보겠습니다
public interface GoTSoftAssertionsProvider extends SoftAssertionsProvider {
    // custom assertions
    // 사용자 정의 검증문
    default GoTCharacterAssert assertThat(GoTCharacter actual) {
        return proxy(GoTCharacterAssert.class, GoTCharacter.class, actual);
    }
}
```

**Step 2**

In order to get a concrete entry point exposing all custom entry points,
create a class implementing all custom `SoftAssertionsProvider` and extending `AbstractSoftAssertions`.
`AbstractSoftAssertions` provides the core internal implementation to collect all errors
from the different implemented entry points (it also implements `SoftAssertionsProvider`).

> 모든 사용자 정의 entry points를 사용하는 구체적인 진입점을 얻으려면
> 모든 사용자 정의 `SoftAssertionsProvider`를 구현하는 클래스를 만들고 `AbstractSoftAssertions`를 상속받습니다.
> `AbstractSoftAssertions`는 구현된 여러 entry point를 통해 모든 오류를 수집하는 핵심 내부 구현을 제공합니다. (`SoftAssertionsProvider`도 구현합니다)

To get standard soft assertions, inherit from `SoftAssertions` instead of `AbstractSoftAssertions`
(or `BddSoftAssertions` to get the BDD flavor).

> 표준 soft assertion을 얻으려면 AbstractSoftAssertions 대신 SoftAssertions을 상속받습니다. (BDD 특성을 얻으려면 BddSoftAssertions을 상속받습니다)

Let’s define our concrete entry points implementing both
`TolkienSoftAssertionsProvider` and `GoTSoftAssertionsProvider`:

> `TolkienSoftAssertionsProvider`와 `GoTSoftAssertionsProvider`를 모두 구현하는 구체적인 entry point를 정의해 보겠습니다:

``` java
// we extend SoftAssertions to get standard soft assertions
// SoftAssertion을 확장하여 표준 soft assertion을 얻습니다
public class FantasySoftAssertions extends SoftAssertions
implements TolkienSoftAssertionsProvider, GoTSoftAssertionsProvider {

    // we can even add more assertions here
    // 우리는 더 많은 검증문을 추가할 수도 있습니다
    public HumanAssert assertThat(Human actual) {
        return proxy(HumanAssert.class, Human.class, actual);
    }
}
```

**Step 3**

The last step is to use `FantasySoftAssertions`:

> 마지막 단계는 FantasySoftAssertion을 사용하는 것입니다:

``` java
FantasySoftAssertions softly = new FantasySoftAssertions();

// custom TolkienCharacter assertions
// 사용자 정의 TolkienCharacter
softly.assertThat(frodo).hasRace(HOBBIT);

// custom GoTCharacter assertions
// 사용자 정의 GoTCharacter
softly.assertThat(nedStark).isDead();

// standard assertions
// 표준 assertions
softly.assertThat("Games of Thrones").startsWith("Games")
                                     .endsWith("Thrones");
// verify assertions
// 검증문을 확인합니다
softly.assertAll();
```

> 사용자 정의 soft assertion(entry point)을 만드는 세 단계를 설명하고 있습니다.
> 각 단계는 AssertJ를 확장하여 원하는 만큼 많은 사용자 정의 entry point를 노출시키는 과정입니다.

> 첫 번째 단계는 인터페이스를 확장하여 필요한 만큼 많은 사용자 정의 진입점을 노출시키는 것입니다.
> 위 예시에서 TolkienSoftAssertionsProvider는 TolkienCharacter 객체에 대한 어서션을 위한 진입점을 노출시키고, GoTSoftAssertionsProvider는 GoTCharacter 객체에 대한 어서션을 위한 진입점을 노출시킵니다.
> 사용자는 필요에 따라 진입점을 추가하거나 수정할 수 있습니다.

> 두 번째 단계는 모든 사용자 정의 SoftAssertionsProvider를 구현하고 AbstractSoftAssertions를 확장하는 클래스를 만드는 것입니다. 
> FantasySoftAssertions은 TolkienSoftAssertionsProvider, GoTSoftAssertionsProvider를 implement하고 SoftAssertions을 extends 하고 있습니다.
> Human 객체에 대한 어서션도 추가적으로 제공하고 있습니다.

> 마지막 단계는 FantasySoftAssertions를 사용하는 것입니다.
> FantasySoftAssertions 인스턴스를 생성하고 사용자 정의 어서션 및 기본 어서션을 수행할 수 있습니다.

**Optional step: use `SoftAssertionsExtension`**

JUnit 5 `SoftAssertionsExtension` calls `softly.assertAll()` after each test
so that we don’t have to do it manually.
Since 3.16.0 it is capable of injecting any `SoftAssertionsProvider`,
we can then inject our custom `FantasySoftAssertions`:

> JUnit5의 `SoftAssertionsExtension`은 `softly.assertAll()`을 각 테스트 이후에 호출하기 때문에 우리가 명시적으로 호출하지 않아도 됩니다. 
> 3.16.0 버전부터 모든 `SoftAssertionsProvider`를 주입할 수 있으므로 사용자 지정 `FantasySoftAssertions` 또한 주입할 수 있습니다.

``` java
@ExtendWith(SoftAssertionsExtension.class) // 확장
public class JUnit5_StandardAndCustomSoftAssertionsExamples {

    @Test
    public void successful_junit_soft_custom_assertion_example(FantasySoftAssertions softly) { // 주입
        softly.assertThat(frodo).hasName("Frodo")
                                .hasAge(33);
        softly.assertThat(frodo.age).isEqualTo(33);
    }
}
```

### Reacting to collected soft assertions

AssertJ allows to perform an action after an `AssertionError` is collected.

> AssertJ는 `AssertionError`가 수집된 이후에 작업을 수행할 수 있습니다.

The action is specified by the `AfterAssertionErrorCollected` functional interface
which can be expressed as lambda, to register your callback call
`setAfterAssertionErrorCollected` as shown below:

> 아래 예시와 같이 작업은 콜백 호출 `setAfterAssertionErrorCollected`를 등록하는 람다를 표현할 수 있는 `AfterAssertionErrorCollected`라는 함수형 인터페이스에 의해 지정됩니다.

Example:

``` java
SoftAssertions softly = new SoftAssertions();
StringBuilder reportBuilder = new StringBuilder(format("Assertions report:%n"));

// register our callback
// callback 등록
softly.setAfterAssertionErrorCollected(error -> reportBuilder.append(format("------------------%n%s%n", error.getMessage())));
// the AssertionError corresponding to the failing assertions are registered in the report
// 실패한 어설션에 해당하는 AssertionError가 보고서에 등록됩니다
softly.assertThat("The Beatles").isEqualTo("The Rolling Stones");
softly.assertThat(123).isEqualTo(123)
                      .isEqualTo(456);
```

resulting `reportBuilder`:

> `reportBuilder`의 결과:

``` 
Assertions report:
------------------
Expecting:
  <"The Beatles">
to be equal to:
  <"The Rolling Stones">
but was not.
------------------
Expecting:
  <123>
to be equal to:
  <456>
but was not.
```

Alternatively, if you have defined your own `SoftAssertions` class and
inherited from `AbstractSoftAssertions`, you can instead override `onAssertionErrorCollected(AssertionError)`.

> 또는 자체 `SoftAssertions` 클래스를 정의하고 `AbstractSoftAssertions`을 상속한 경우, onAssertionErrorCollected(AssertionError)를 재정의할 수 있습니다.

Example:

``` java
class TolkienSoftAssertions extends AbstractSoftAssertions {

public TolkienHeroesAssert assertThat(TolkienHero actual) {
    return proxy(TolkienHeroesAssert.class, TolkienHero.class, actual);
}

    @Override
    public void onAssertionErrorCollected(AssertionError assertionError) {
        System.out.println(assertionError);
    }
}

TolkienSoftAssertions softly = new TolkienSoftAssertions();

TolkienCharacter frodo = TolkienCharacter.of("Frodo", 33, HOBBIT);

// the AssertionError corresponding to this failing assertion is printed to the console.
// 실패한 검증에 해당하는 AssertionError가 콘솔에 출력됩니다
softly.assertThat(frodo).hasName("Bilbo");
```
