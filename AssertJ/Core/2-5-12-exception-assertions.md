## 2.5.12. Exception Assertions

This chapter answers the question: how to assert that an exception has been thrown
and check that it is the expected one?

> 이번 챕터는 다음과 같은 질문에 대해 답합니다: 어떻게 예외가 발생했음을 검증하고 예상한 예외인지 확인할 수 있을까?

<br/>

### Checking exception message

**예외 메시지 확인하기**

There are various ways for checking the exception message content,
you can check the exact message, what it contains, its start, its end, if it matches a regex.

> 예외 메시지의 내용을 확인하는 다양한 방법이 존재합니다.
> 여러분은 정확한 메시지를 확인할 수도, 메시지가 포함하고 있는 것을 확인할 수도, 메시지가 어떻게 시작하는지, 어떻게 끝나는지 혹은 정규표현식과 일치하는지도 확인할 수 있습니다.

Most of the assertions expecting a `String` can use the String.format syntax.

> 대부분의 `String` 검증은 String.format을 사용하여 할 수 있습니다.

Examples:

``` java
Throwable throwable = new IllegalArgumentException("wrong amount 123");

assertThat(throwableWithMessage).hasMessage("wrong amount 123")
                                .hasMessage("%s amount %d", "wrong", 123)
                                // check start
                                // 시작 부분 확인
                                .hasMessageStartingWith("wrong")
                                .hasMessageStartingWith("%s a", "wrong")
                                // check content
                                // 포함 여부 확인
                                .hasMessageContaining("wrong amount")
                                .hasMessageContaining("wrong %s", "amount")
                                .hasMessageContainingAll("wrong", "amount")
                                // check end
                                // 끝 부분 확인
                                .hasMessageEndingWith("123")
                                .hasMessageEndingWith("amount %s", "123")
                                // check with regex
                                // 정규표현식 확인
                                .hasMessageMatching("wrong amount .*")
                                // check does not contain
                                // 미포함 여부 확인
                                .hasMessageNotContaining("right")
                                .hasMessageNotContainingAny("right", "price")
```

<br/>

### Checking the cause and root cause

There are two approaches to check the cause and root cause,
either directly or navigate to it with `getCause()` and `getRootCause()` and check it.

> `getCause()`와 `getRootCause()`을 사용하여 원인과 근본적인 원인을 확인하는 두 가지 접근법이 있습니다.

**Checking the cause**

You can check the cause directly if you know it but that’s not always possible,
in that case you can basically only check its type. This is pretty limited in terms of assertions,
a better approach is to navigate to the cause with `getCause()`
and take advantage of all existing exception assertions.

Direct cause assertion are limited ...

> 만약 당신이 원인을 알고 있다면 직접 확인할 수 있겠지만, 항상 가능한 것은 아니며 기본적으로 타입만 확인할 수 있습니다.
> 상당히 제한적인 검증 방법 대신 `getCause()`를 사용하여 원인을 탐색하고 제공하는 모든 예외 assertion을 활용하는 것을 권장합니다.

> 직접적인 원인 검증은 제한적입니다 ...

``` java
NullPointerException cause = new NullPointerException("boom!");
Throwable throwable = new Throwable(cause);

assertThat(throwable).hasCause(cause)
                    // hasCauseInstanceOf will match inheritance.
                    // 상속과 일치
                    .hasCauseInstanceOf(NullPointerException.class)
                    .hasCauseInstanceOf(RuntimeException.class)
                    // hasCauseExactlyInstanceOf will match only exact same type
                    // 정확히 같은 타입인지 검증
                    .hasCauseExactlyInstanceOf(NullPointerException.class);
```

... but navigating to the cause allows to take advantage of all exception assertions:

> 그러나 원인을 탐색하면 모든 예외 관련 assertion을 활용할 수 있습니다:

``` java
// navigate before checking
assertThat(throwable).getCause()
                    .hasMessage("boom!")
                    .hasMessage("%s!", "boom")
                    .hasMessageStartingWith("bo")
                    .hasMessageEndingWith("!")
                    .hasMessageContaining("boo")
                    .hasMessageContainingAll("bo", "oom", "!")
                    .hasMessageMatching("b...!")
                    .hasMessageNotContaining("bam")
                    .hasMessageNotContainingAny("bam", "bim")
                    // isInstanceOf will match inheritance.
                    // 상속과 일치
                    .isInstanceOf(NullPointerException.class)
                    .isInstanceOf(RuntimeException.class)
                    // isExactlyInstanceOf will match only exact same type
                    // 정확히 같은 타입인지 검증
                    .isExactlyInstanceOf(NullPointerException.class);
```                   
                    
An alternative is using `assertThatExceptionOfType` combined with `havingCause`
as shown in the following example:

> 다음 예제와 같이 `havingCause`와 함께 `assertThatExceptionOfType`을 사용하여 검증할 수도 있습니다:

``` java
assertThatExceptionOfType(RuntimeException.class)
        .isThrownBy(() -> { throw new RuntimeException(new IllegalArgumentException("boom!")); })
        .havingCause() // 발생 원인의
        .withMessage("boom!"); // 예외 메시지
```

**Checking the root cause**

**근본 원인을 확인하기**

You can check the root cause directly with `hasRootCause`,
`hasRootCauseMessage` and `hasRootCauseInstanceOf` if you have access to it but that’s not always possible,
this is a bit limited in terms of assertions,
a better way is to navigate to the root cause with `getRootCause()`
and take advantage of all existing exception assertions.

> 당신이 접근 가능하다면 `hasRootCause`, `hasRootCauseMessage`, `hasRootCauseInstanceOf`를 사용하여 바로 근본 원인을 확인할 수 있습니다만, 항상 가능한 것은 아닙니다.
> 이는 다소 제한적인 검증 방법입니다.
> 근본 원인에 접근하는 더 나은 방법은 `getRootCause()`를 사용하는 것입니다.
> `getRootCause()`를 사용하면 제공하는 모든 assertion의 이점을 활용할 수 있습니다.

Examples:

``` java
NullPointerException rootCause = new NullPointerException("null!"); // 에러 예시
Throwable throwable = new Throwable(new IllegalStateException(rootCause));

// direct root cause check
// 직접 원인 확인하기
assertThat(throwable).hasRootCause(rootCause)
                    .hasRootCauseMessage("null!")
                    .hasRootCauseMessage("%s!", "null")
                    // hasRootCauseInstanceOf will match inheritance
                    // 해당 클래스의 객체인지 확인 (상속 관계 확인)
                    .hasRootCauseInstanceOf(NullPointerException.class)
                    .hasRootCauseInstanceOf(RuntimeException.class)
                    // hasRootCauseExactlyInstanceOf will match only exact same type
                    // 정확한 예외 클래스 확인
                    .hasRootCauseExactlyInstanceOf(NullPointerException.class);

// navigate to root cause and check
// 근본 원인으로 이동하여 확인 (더 다양한 검증 메서드를 활용할 수 있음)
assertThat(throwable).getRootCause()
                    .hasMessage("null!")
                    .hasMessage("%s!", "null")
                    .hasMessageStartingWith("nu")
                    .hasMessageEndingWith("!")
                    .hasMessageContaining("ul")
                    .hasMessageContainingAll("nu", "ull", "l!")
                    .hasMessageMatching("n...!")
                    .hasMessageNotContaining("NULL")
                    .hasMessageNotContainingAny("Null", "NULL")
                    // isInstanceOf will match inheritance.
                    // 해당 클래스의 객체인지 확인 (상속 관계 확인)
                    .isInstanceOf(NullPointerException.class)
                    .isInstanceOf(RuntimeException.class)
                    // isExactlyInstanceOf will match only exact same type
                    // 정확한 예외 클래스 확인
                    .isExactlyInstanceOf(NullPointerException.class);
```

An alternative is using `assertThatExceptionOfType` combined with `havingRootCause`
as shown in the following example:

> 대안으로는 다음 예시와 같이 `assertThatExceptionOfType`와 `havingRootCause`를 함께 사용하는 방법이 있습니다.

``` java
assertThatExceptionOfType(RuntimeException.class)
                .isThrownBy(() -> { throw new RuntimeException(new IllegalArgumentException(new NullPointerException("root error"))); })
                .havingRootCause()
                .withMessage("root error");
```

**No cause**

**원인이 없는 경우**

You can verify that a Throwable does not have a cause with `hasNoCause()`.

> 예외가 근본 원인을 갖고 있지 않은 경우 `hasNoCause()`를 사용하여 확인할 수 있습니다.

<br/>

### BDD style

BDD aficionados can separate WHEN and THEN steps by using `catchThrowable(ThrowingCallable)`
to capture the `Throwable` and then perform assertions (`ThrowingCallable` is a functional interface
which can be expressed as a lambda).

> BDD 애호가라면, `Throwable`을 잡아 검증을 수행하는 `catchThrowable(ThrowingCallable)`을 사용하여 WHEN, THEN과 같이 스탭을 나눌 수 있습니다.
> `ThrowingCallable`은 람다를 표현하기 위한 함수형 인터페이스입니다.

Example:

``` java
// GIVEN
String[] names = { "Pier ", "Pol", "Jak" };
// WHEN
Throwable thrown = catchThrowable(() -> System.out.println(names[9])); // 발생한 예외를 잡아서 변수에 할당 : WHEN을 작성할 수 있게 됩니다.
// THEN
then(thrown).isInstanceOf(ArrayIndexOutOfBoundsException.class)
           .hasMessageContaining("9");
    
// assertThat is also available but is less "BDD style"
assertThat(thrown).isInstanceOf(ArrayIndexOutOfBoundsException.class)
           .hasMessageContaining("9");
```

`catchThrowable` returns null if no exception is thrown,
but there is a better way to check that no exception is thrown.

> 만약 던져진 예외가 없다면 `catchThrowable`은 null을 반환합니다.
> 하지만 아무 예외도 발생하지 않음을 확인하는 더 나은 방법도 존재합니다.

`catchThrowableOfType` is a variation of `catchThrowable` where the caught exception type is
verified and returned allowing to check the custom exception fields/properties.

> `catchThrowableOfType`은 잡은 예외의 타입을 확인하고 사용자 정의 예외 필드/속성을 확인한 뒤 반환하는 `catchThrowable`의 변형입니다.

<br/>

### assertThatThrownBy

`assertThatThrownBy(ThrowingCallable)` is an alternative to `catchThrowable`,
use it if you find more readable.

> `assertThatThrownBy(ThrowingCallable)`은 `catchThrowable`보다 가독성이 좋은 대체 가능한 메서드입니다.

``` java
Example:

assertThatThrownBy(() -> { throw new Exception("boom!"); }).isInstanceOf(Exception.class) // 예외 발생 시 타입 검증
                  .hasMessageContaining("boom"); // 예외 메시지 검증
```

If the provided `ThrowingCallable` does not raise an exception, an assertion error is immediately thrown.

> 만약 `ThrowingCallable`이 예외를 발생시키지 않으면 즉시 검증 오류가 발생합니다.

<br/>

### assertThatExceptionOfType

`assertThatExceptionOfType` is an alternative syntax that some people find more natural.

> `assertThatExceptionOfType`은 일부 사용자들이 더 자연스럽게 사용하는 대체 가능한 메서드입니다.

``` java
assertThatExceptionOfType(IOException.class).isThrownBy(() -> { throw new IOException("boom!"); })
            .withMessage("%s!", "boom")
            .withMessageContaining("boom")
            .withNoCause();
```

If the provided `ThrowingCallable` does not raise an exception, an assertion error is immediately thrown.

> 만약 `ThrowingCallable`이 예외를 발생시키지 않으면 즉시 검증 오류가 발생합니다.

Similarly to `catchThrowableOfType`, the latter syntax has been enriched for commonly used exceptions:

> `catchThrowableOfType`과 유사하게 다음의 구문들은 사용하여 예외 검증을 보다 강화할 수 있습니다.

- `assertThatNullPointerException`
- `assertThatIllegalArgumentException`
- `assertThatIllegalStateException`
- `assertThatIOException`

The previous example can be rewritten as:

> 이전 예제는 다음과 같이 다시 작성할 수 있습니다:

``` java
assertThatIOException().isThrownBy(() -> { throw new IOException("boom!"); }) // assertThatIOException()의 사용으로 강화된 검증
                        .withMessage("%s!", "boom")
                        .withMessageContaining("boom")
                        .withNoCause();
```

<br/>

### Testing that no exception is thrown

You can test that a piece of code does not throw any exception with:

> 다음을 사용하여 코드가 예외를 던지지 않는지 확인할 수 있습니다:

``` java
// standard style
assertThatNoException().isThrownBy(() -> System.out.println("OK"));
// BDD style
thenNoException().isThrownBy(() -> System.out.println("OK"));
```

or similarly:

> 또는 다음과 같이 작성할 수도 있습니다:

``` java
// standard style
assertThatCode(() -> System.out.println("OK")).doesNotThrowAnyException(); // 어떠한 예외도 발생하지 않음
// BDD style
thenCode(() -> System.out.println("OK")).doesNotThrowAnyException();
```

