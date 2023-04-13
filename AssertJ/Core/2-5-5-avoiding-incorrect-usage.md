## 2.5.5. Avoiding incorrect usage

**잘못된 사용을 피하기**

There are a few things to keep in mind when using AssertJ to avoid misusing it.

> AssertJ를 잘못 사용하지 않도록 염두에 두어야 할 몇 가지 사항이 있습니다.

<br/>

### Forgetting to call an assertion

**assertion을 호출하는 것을 잊는 것**

The main trap is to pass the object under to test to `assertThat()`
and forget to call an assertion afterward. This misuse can be detected by
SpotBugs or Findbugs thanks to the `@CheckReturnValue` annotation on all `assertThat()` methods.

> 주된 실수 중 하나는 테스트할 객체를 `assertThat()`에 전달하고 나중에 assertion을 호출하지 않는 것입니다.

Here’s what it looks like in SpotBugs:

![img.png](https://assertj.github.io/doc/images/spotbugs-invalid-assertj-usage-detection.png)

_Figure 1. SpotBugs detecting AssertJ invalid usage_

The following examples show incorrect AssertJ API usage to avoid!

> 다음 예는 피해야 할 잘못된 AssertJ API 사용입니다!

**Bad**

``` java
// DON'T DO THIS ! It does not assert anything
// 이렇게 하지마세요! 아무런 assertion이 없습니다.
assertThat(actual.equals(expected)); // 객체를 담아야하는 assertThat에 assertion까지 작성한 오류
```

**Good**

``` java
// DO THIS:
// 이렇게 하세요:
assertThat(actual).isEqualTo(expected); // assertThat에 객체를 담고 이어서 assertion 호출

// OR THIS (less classy but ok):
// 이렇게도 가능합니다 (고급스럽지는 않지만 나쁘지 않습니다):
assertThat(actual.equals(expected)).isTrue(); // 'boolean 값이 true이다'라는 assertion 작성
```

**Bad**

``` java
// DON'T DO THIS ! It does not assert anything and passes
// 이렇게 하지마세요! 아무것도 주장하지 않고 통과합니다.
assertThat(1 == 2);
```

**Good**

``` java
// DO THIS: (fails as expected)
// 이렇게 하세요: (실패가 예상됩니다)
assertThat(1).isEqualTo(2);

// OR THIS (less classy but ok):
// 이렇게도 가능합니다 (고급스럽지는 않지만 나쁘지 않습니다):
assertThat(1 == 2).isTrue();
```

<br/>

### Calling as() after the assertion

**assertion 후에 `as()`를 호출하는 것**

Describing an assertion must be done before calling the assertion
otherwise it is ignored as a failing assertion breaks will prevent the call to `as()`.

> assertion을 호출하기 전에 assertion을 설명해야 합니다. 그렇지 않으면 assertion 중단이 실패하면서 `as()`에 대한 호출이 차단되므로 무시됩니다.

**Bad**

``` java
// DON'T DO THIS ! as/describedAs have no effect after the assertion
// 이렇게 하지마세요! as 또는 describedAs는 assertion 뒤에서 어떠한 작동도 하지 않습니다.
assertThat(actual).isEqualTo(expected).as("description");
assertThat(actual).isEqualTo(expected).describedAs("description");
```

**Good**

``` java
// DO THIS: use as/describedAs before the assertion
// 이렇게 하세요: assertion 호출 전에 as 또는 describedAs를 사용하세요.
assertThat(actual).as("description").isEqualTo(expected);
assertThat(actual).describedAs("description").isEqualTo(expected);
```

<br/>

### Calling withFailMessage/overridingErrorMessage after the assertion

**assertion 후에 withFailMessage 또는 overridingErrorMessage를 호출하는 것**

Setting an error message must be done before calling the assertion
otherwise it is ignored as a failing assertion breaks will prevent
the call to `withFailMessage()` / `overridingErrorMessage()`.

> 에러 메시지는 반드시 assertion을 호출하기 전에 설정해야 합니다.
> 그렇지 않으면 assertion 중단 실패로 인해 `usingComparator()` 또는 `usingElementComparator()` 호출이 차단되므로 무시됩니다.

**Bad**

``` java
// DON'T DO THIS ! overridingErrorMessage/withFailMessage have no effect after the assertion
// 이렇게 하지 마세요! assertion을 호출한 뒤에 사용한 overridingErrorMessage 또는 withFailMessage는 작동하지 않습니다.
assertThat(actual).isEqualTo(expected).overridingErrorMessage("custom error message");
assertThat(actual).isEqualTo(expected).withFailMessage("custom error message");
```

**Good**

``` java
// DO THIS: use overridingErrorMessage/withFailMessage before the assertion
// 이렇게 하세요: assertion을 호출하기 전에 overridingErrorMessage 또는 withFailMessage를 사용하세요.
assertThat(actual).overridingErrorMessage("custom error message").isEqualTo(expected);
assertThat(actual).withFailMessage("custom error message").isEqualTo(expected);
```

<br/>

### Setting a comparator after the assertion

**assertion 선언 후 comparator를 설정하는 것**

Setting comparators must be done before calling the assertion
otherwise it is ignored as a failing assertion breaks will prevent
the call to `usingComparator()` / `usingElementComparator()`.

> comparator는 반드시 assertion을 호출하기 전에 설정해야 합니다. 
> 그렇지 않으면 assertion 중단 실패로 인해 `usingComparator()` 또는 `usingElementComparator()` 호출이 차단되므로 무시됩니다.

**Bad**

``` java
// DON'T DO THIS ! Comparator is not used
// 이렇게 하지 마세요! Comparator를 사용하지 않습니다.
assertThat(actual).isEqualTo(expected).usingComparator(new CustomComparator());
```

**Good**

``` java
// DO THIS:
// 이렇게 하세요:
assertThat(actual).usingComparator(new CustomComparator()).isEqualTo("a"); // 항상 순서를 신경써서 사용
```
