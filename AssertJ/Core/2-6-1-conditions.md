## 2.6.1. Conditions

Assertions can be extended by using conditions, to create a condition you just have
to implement `org.assertj.assertions.core.Condition` and its unique method matches.

> 검증문은 condition을 통해 확장할 수 있습니다.
> condition을 생성하려면 `org.assertj.assertions.core.Condition` 인터페이스를 구현하고
> 해당 인터페이스의 matches 메서드를 구현해야 합니다.

Once your Condition is created, you can use it with methods :

> Condition을 생성하면 메서드와 함께 사용할 수 있습니다:

`is(myCondition)` or `has(myCondition)`, both verifying that the condition is met
(hint: pick the one that makes your code more readable).

> `is(myCondition)` 또는 `has(myCondition)`, 둘 다 조건에 충족되는지 확인합니다.(코드를 더 읽기 쉽게 만드는 메서드를 선택하세요)

Example:

``` java
// jedi is a Condition
// jedi는 Condition입니다
assertThat("Yoda").is(jedi);
```

Conditions can be combined with :

> Condition은 결합할 수 있습니다:

- `not(Condition)`: given condition must not be met

- `allOf(Condition...)`: all given conditions must be met

- `anyOf(Condition...)`: one of the given conditions must be met

> - `not(Condition)`: 주어진 조건을 충족하지 않아야 합니다
> - `allOf(Condition...)`: 주어진 조건을 모두 충족해야 합니다
> - `anyOf(Condition...)`: 주어진 조건 중 하나는 충족해야 합니다

You can verify that a Condition is met on elements of a collection, with the following methods:

> 다음 방법을 사용하여 컬렉션의 요소들이 조건을 충족하는지 확인할 수 있습니다:

- `are(condition)/have(condition)`: all elements must meet the given condition

- `areAtLeast(n, condition)`/`haveAtLeast(n, condition)`: at least n elements must meet the given condition

- `areAtMost(n, condition)`/`haveAtMost(n, condition)`: no more than n elements must meet the given condition

- `areExactly(n, condition)`/`haveExactly(n, condition)`: exactly n elements must meet the given condition

> - `are(condition)/have(condition)`: 모든 요소들이 다음의 조건을 충족해야 합니다
> - `areAtLeast(n, condition)`/`haveAtLeast(n, condition)`: 적어도 n개의 요소는 다음의 조건을 충족해야 합니다
> - `areAtMost(n, condition)`/`haveAtMost(n, condition)`: n개 이하의 요소들이 다음의 조건을 충족해야 합니다
> - `areExactly(n, condition)`/`haveExactly(n, condition)`: 정확히 n개의 요소들이 다음의 조건을 충족해야 합니다

Moreover all Condition related methods have their negation counterpart,
`is`/`isNot`, `have`/`doesNotHave`, `are`/`areNot`, ...

> 또한 모든 Condition 관련 메서드에는 `is`/`isNot`, `have`/`doesNotHave`, `are`/`areNot`와 같이 대응되는 부정 검증문이 있습니다

The examples below are from assertj-examples and more specifically UsingConditionExamples.java.

### Creating a Condition

Let’s define two similar conditions: `jedi` and `jediPower` with the same implementation
to show that code readability is better when using `jedi` with `is` and jediPower with `has`.

> `jedi`는 `is`와 함께 사용하고 `jediPower`는 `has`와 함께 사용할 때 코드 가독성이 더 좋다는 것을 보여주기 위해
> 두 가지 유사한 조건을 정의해 보겠습니다:

``` java
import static org.assertj.core.util.Sets.newLinkedHashSet;

static Set<String> JEDIS = newLinkedHashSet("Luke", "Yoda", "Obiwan");

// implementation with Java 8 lambda
// lambda를 사용하여 구현
Condition<String> jediPower = new Condition<>(JEDIS::contains, "jedi power");

// implementation with Java 7
// lambda를 사용하지 않고 Condition을 구현한 다음 matches 메서드를 구현
Condition<String> jedi = new Condition<String>("jedi") {
    @Override
    public boolean matches(String value) {
        return JEDIS.contains(value);
    }
};
```

The String parameter is describing the condition, this is used in error messages.

> String 매개변수는 조건을 설명하며 오류 메시지에 사용됩니다.

### Using Conditions

Usage with single instances:

> 단일 인스턴스 사용:

``` java
assertThat("Yoda").is(jedi);
assertThat("Vader").isNot(jedi);

assertThat("Yoda").has(jediPower);
assertThat("Solo").doesNotHave(jediPower);
```

The Condition description is used in error messages, ex:

> 조건 설명은 다음과 같은 오류 메시지에 사용됩니다:
> 
```
assertThat("Vader").is(jedi);
```

will fail with the following error message:

> 다음 오류 메시지와 함께 실패합니다:

```
"expecting:<'Vader'> to be:<jedi>"
```

Usage with collections:

> 컬렉션에서의 사용:

``` java
assertThat(list("Luke", "Yoda")).are(jedi);
assertThat(list("Leia", "Solo")).areNot(jedi);

assertThat(list("Luke", "Yoda")).have(jediPower);
assertThat(list("Leia", "Solo")).doNotHave(jediPower);

assertThat(list("Luke", "Yoda", "Leia")).areAtLeast(2, jedi);
assertThat(list("Luke", "Yoda", "Leia")).haveAtLeast(2, jediPower);

assertThat(list("Luke", "Yoda", "Leia")).areAtMost(2, jedi);
assertThat(list("Luke", "Yoda", "Leia")).haveAtMost(2, jediPower);

assertThat(list("Luke", "Yoda", "Leia")).areExactly(2, jedi);
assertThat(list("Luke", "Yoda", "Leia")).haveExactly(2, jediPower);
```

### Combining Conditions

Conditions can be combined with `allOf(Condition...)` (logical and) or `anyOf(Condition...)` (logical or),
`not` can be used to invert one.

> 조건은 `allOf(Condition...)` (논리적 and) 또는 `anyOf(Condition...)` (논리적 or) 와 결합될 수 있으며,
> 하나를 뒤집는 데 `not`을 사용할 수 있습니다.

Let’s define a sith condition:

> sith 조건을 정의해보겠습니다:

``` java
List<String> SITHS = list("Sidious", "Vader", "Plagueis");
Condition<String> sith = new Condition<>(SITHS::contains, "sith");
```

We can write these assertions:

> 다음과 같은 검증문을 작성할 수 있습니다:

``` java
assertThat("Vader").is(anyOf(jedi, sith));
assertThat("Solo").is(allOf(not(jedi), not(sith)));
```
