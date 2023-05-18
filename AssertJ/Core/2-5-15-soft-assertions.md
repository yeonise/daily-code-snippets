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
