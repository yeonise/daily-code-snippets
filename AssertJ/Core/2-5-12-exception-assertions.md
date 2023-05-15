## 2.5.12. Exception Assertions

This chapter answers the question: how to assert that an exception has been thrown
and check that it is the expected one?

> 이번 챕터는 다음과 같은 질문에 대해 답합니다: 어떻게 예외가 발생했음을 검증하고 예상한 예외인지 확인할 수 있을까?

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

