## 2.5.16. Assumptions

Assumptions provide support for conditional test execution,
if the assumptions are met the test is executed normally,
if they don’t the test is aborted and marked as ignored.

> Assumptions은 조건부 테스트 실행을 제공합니다.
> 만약 가정이 맞다면 테스트는 정상적으로 실행되고,
> 가정이 틀렸다면 테스트는 중단되고 무시됩니다.

Assumptions are typically used whenever it does not make sense to continue
execution of a given test method — a typical usage is running tests depending on a given OS/environment.

> Assumptions은 일반적으로 주어진 테스트를 계속 실행하기에 타당하지 않을 때 사용합니다.
> 일반적으로 주어진 운영체제나 환경에 따라 테스트를 실행할 때 사용합니다.

All AssertJ assumptions are static methods in the `Assumptions` class,
they match the assertion API but are names `assumeThat` instead of `assertThat`.
You can also get assumptions through the `WithAssumptions` interface.

> 모든 AssertJ assumption은 `Assumptions` 클래스의 정적 메서드로,
> assertion API와 일치하지만 이름은 `assertThat` 대신 `assumeThat`을 사용합니다.
> `WithAssumptions` 인터페이스를 통해 가정문을 얻을 수도 있습니다.

Example resulting in the test to be ignored:

> 테스트가 무시되는 예:

``` java
@Test
public void when_an_assumption_is_not_met_the_test_is_ignored() {
    // since this assumption is obviously false ...
    // 이 가정은 틀렸기 때문에
    assumeThat(frodo.getRace()).isEqualTo(ORC);
    // ... this assertion is not performed
    // 이후의 assertThat은 수행되지 않습니다
    assertThat(fellowshipOfTheRing).contains(sauron);
}
```

Example resulting in the test to be executed normally:

> 정상적으로 테스트가 진행되는 예:

``` java
@Test
public void when_all_assumptions_are_met_the_test_is_run_normally() {
    // since this assumption is true ...
    // 이 가정은 옳기 때문에
    assumeThat(frodo.getRace()).isEqualTo(HOBBIT);
    // ... this assertion is performed
    // assertion이 정상적으로 수행됩니다
    assertThat(fellowshipOfTheRing).doesNotContain(sauron);
}
```
