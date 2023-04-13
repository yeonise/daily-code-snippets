## 2.5.3. Assertion Description

AssertJ tries its best to give helpful error messages,
but you can always change it with `overridingErrorMessage()` or `withFailMessage()`.

> AssertJ는 유용한 에러 메시지를 제공하기 위해 최선을 다하지만,
> 언제든지 `overridingErrorMessage()` 또는 `withFailMessage()`로 에러 메시지를 변경할 수 있습니다.

Example with this failing test:

``` java
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);
TolkienCharacter sam = new TolkienCharacter("Sam", 38, Race.HOBBIT);

// failing assertion, remember to call withFailMessage/overridingErrorMessage before the assertion!
// 항상 assertion 전에 호출해야 한다는 것을 잊지마세요!
assertThat(frodo.getAge()).withFailMessage("should be %s", frodo) // 실패할 경우 출력할 메시지 재정의
                          .isEqualTo(sam);
```
The error message is:

> 실패한 테스트의 에러 메시지:

``` text
java.lang.AssertionError: should be TolkienCharacter [name=Frodo, age=33, race=HOBBIT]
```

<br/>

### Lazy error message overriding

**지연된 오류 메시지 재정의**

If the error message is expensive to build,
use the overloaded methods taking a `Supplier<String>` instead of a `String`,
the message will only be built if the assertion fails.

> 오류 메시지를 작성하는 데 비용이 많이 드는 경우에는 `String` 대신 `Supplier<String>`을 사용하는 메서드를 사용합니다.
> 그러면 assertion이 실패하는 경우에만 에러 메시지를 생성합니다.

Example:

``` java
// 연산이 수행될 때 처리하는 stream과 유사
assertThat(player.isRookie()).overridingErrorMessage(() -> "Expecting Player to be a rookie but was not.")
.isTrue();

assertThat(player.isRookie()).withFailMessage(() -> "Expecting Player to be a rookie but was not.")
.isTrue();
```
