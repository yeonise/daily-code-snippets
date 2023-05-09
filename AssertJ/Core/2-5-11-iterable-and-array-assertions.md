## 2.5.11. Iterable and array assertions

### Checking iterables/arrays content

**iterable 객체 또는 배열의 내용 확인하기**

There are different flavors of `contains` assertion, here’s a table to help choose the most relevant one:

> 다양한 형태의 `contains` assertion이 있습니다. 다음은 당신이 가장 관련성이 높은 것을 선택하는 데 도움이 되는 표입니다.

| Assertion                   | Description                                                                                                                                                                                |
|:----------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `contains`                  | Verifies that the actual iterable/array contains the given values in any order                                                                                                             |
| `containsOnly`              | Verifies that the actual group contains only the given values and nothing else in any order and ignoring duplicates (i.e. once a value is found, its duplicates are also considered found) |
| `containsExactly`           | Verifies that the actual iterable/array contains exactly the given values and nothing else in order                                                                                        |
| `containsExactlyInAnyOrder` | Verifies that the actual iterable/array contains exactly the given values and nothing else in any order                                                                                    |
| `containsSequence`          | Verifies that the actual group contains the given sequence in the correct order and without extra values between the sequence values                                                       |
| `containsSubsequence`       | Verifies that the actual group contains the given subsequence in the correct order possibly with other values between them                                                                 |
| `containsOnlyOnce`          | Verifies that the actual iterable/array contains the given values only once                                                                                                                |
| `containsAnyOf`             | Verifies that the actual iterable/array contains at least one of the given values (like an or operator on the given values)                                                                |

<br/>

| Assertion                   | Description                                                        |
|:----------------------------|:-------------------------------------------------------------------|
| `contains`                  | 순서에 상관없이 값을 포함하고 있는지 확인합니다.                                        |
| `containsOnly`              | 중복은 무시하고 주어진 값만 포함하고 있는지 확인합니다. (즉, 값이 발견되면 중복 된 값도 찾은 것으로 여겨집니다.) |
| `containsExactly`           | 순서에 맞게 주어진 값을 정확히 포함하고 있는지 확인합니다.                                  |
| `containsExactlyInAnyOrder` | 순서와 상관없이 주어진 값을 정확히 포함하고 있는지 확인합니다.                                |
| `containsSequence`          | 주어진 그룹을 순서에 맞게 포함하고 있는지 확인합니다. 중간에 다른 객체가 들어가 순서가 달라지면 실패합니다.      |
| `containsSubsequence`       | 주어진 순서에 맞게 객체가 나열되어 있는지 확인합니다. 중간에 다른 객체가 들어가 있을 수 있습니다.           |
| `containsOnlyOnce`          | 해당 값을 단 하나만 갖고 있는지 확인합니다.                                          |
| `containsAnyOf`             | 주어진 값을 적어도 한 개 이상 포함하고 있는지 확인합니다.                                  |

<br/>

### Verify assertions on some elements

**특정 요소에 대한 assertion 확인하기**

**Satisfy**

**만족 여부**

You can assert that all or any elements verify the given assertions
with `allSatisfy` and `anySatisfy`, conversely `noneSatisfy` lets you assert
that no elements verify the given assertions.

> 당신은 모든 요소 또는 일부 요소에 대해 `모두 만족`, `하나라도 만족`, `하나도 만족하지 않음`을
> `allSatisfy`, `anySatisfy`, `noneSatisfy`를 통해 확인할 수 있습니다.

The given assertions are expressed with a `Consumer` (typically with a lambda).

> 주어진 assertion들은 일반적으로 람다를 사용한 `Consumer`를 통해 표현됩니다.

Examples:

``` java
List<TolkienCharacter> hobbits = list(frodo, sam, pippin);

// all elements must satisfy the given assertions
// 모든 요소들이 아래의 주장을 모두 만족해야 함
assertThat(hobbits).allSatisfy(character -> {
  assertThat(character.getRace()).isEqualTo(HOBBIT);
  assertThat(character.getName()).isNotEqualTo("Sauron");
});

// at least one element must satisfy the given assertions
// 하나라도 아래의 주장을 모두 만족해야 함
assertThat(hobbits).anySatisfy(character -> {
  assertThat(character.getRace()).isEqualTo(HOBBIT);
  assertThat(character.getName()).isEqualTo("Sam");
});

// no element must satisfy the given assertions
// 어떤 요소도 아래의 주장을 만족하지 않음
assertThat(hobbits).noneSatisfy(character -> assertThat(character.getRace()).isEqualTo(ELF));
```

**Match**

**일치 여부**

You can assert that all or any elements match the given `Predicate` with `allMatch` and `anyMatch`,
conversely `noneMatch` lets you assert that no elements verify the given predicate.

> 주어진 `Predicate`에 대해 모든 요소 또는 일부 요소가 `모두 일치`, `하나라도 일치`, `하나도 일치하지 않음`을
> `allMatch`, `anyMatch`, `noneMatch`를 통해 확인할 수 있습니다.

Examples:

``` java
List<TolkienCharacter> hobbits = list(frodo, sam, pippin);

assertThat(hobbits).allMatch(character -> character.getRace() == HOBBIT, "hobbits") // 모든 race가 hobbits인지 확인
                   .anyMatch(character -> character.getName().contains("pp")) // pp라는 이름을 갖는 요소가 1개라도 존재하는지 확인
                   .noneMatch(character -> character.getRace() == ORC); // race가 ORC인 요소가 하나도 없는지 확인
```

You can pass a predicate description to make the error message more explicit if the assertion fails.

> assertion이 실패할 경우 설명을 전달하여 오류 메시지를 더 명확하게 만들 수 있습니다.

<br/>

### Navigating to a given element

**주어진 요소로 이동하기**

The idea is to navigate to a given element in order to check it,
you can navigate to the first, last or any element by index or
if you expect only one element use `singleElement`.

> 주어진 요소로 이동하여 확인합니다.
> 첫 번째, 마지막 또는 임의의 인덱스로 이동하거나
> 단일 요소만 사용하는 경우에 탐색할 수 있습니다.

this is only available for iterables at the moment.

> iterables한 경우에만 사용할 수 있습니다.

**First / last / element(index)**

**처음 / 마지막 / 인덱스**

Use `first`, `last` and `element(index)` to navigate to the corresponding element,
after navigating you can only use object assertions unless you have specified an Assert class
or preferrably an `InstanceOfAssertFactory` as shown in the following examples.

> `first`, `last` 그리고 `element(index)`를 사용하여 해당 요소로 이동할 수 있습니다.
> 탐색 후에는 아래 예제와 같이 Assert 클래스 또는 `InstanceOfAssertFactory`를 지정하지 않는 한 객체에 대한 assertion만 사용할 수 있습니다.

Examples:

``` java
// only object assertions available after navigation
// assertion은 navigation 이후에 사용 가능
Iterable<TolkienCharacter> hobbits = list(frodo, sam, pippin);
assertThat(hobbits).first().isEqualTo(frodo); // 첫 번째 객체는 frodo인지 확인
assertThat(hobbits).element(1).isEqualTo(sam); // 인덱스 1에 위치한 요소가 sam인지 확인
assertThat(hobbits).last().isEqualTo(pippin); // 마지막 객체는 pippin인지 확인

// strongly typed String assertions after navigation
// 강한 String assertion의 사용
Iterable<String> hobbitsName = list("frodo", "sam", "pippin");
// STRING is an InstanceOfAssertFactory from org.assertj.core.api.InstanceOfAssertFactories.STRING
// as() is just synthetic sugar for readability
// as()는 가독성을 위해 사용한 것
assertThat(hobbitsName).first(as(STRING))
                        .startsWith("fro")
                        .endsWith("do");
assertThat(hobbitsName).element(1, as(STRING))
                        .startsWith("sa")
                        .endsWith("am");
assertThat(hobbitsName).last(as(STRING))
                        .startsWith("pip")
                        .endsWith("pin");

// alternative for strongly typed assertions
// 강한 assertion의 대안
assertThat(hobbitsName, StringAssert.class).first()
                                            .startsWith("fro")
                                            .endsWith("do");
```

**Single element**

**단일 객체**

`singleElement` checks that the iterable has only one element and navigates to it,
after navigating you can only use object assertions unless you have specified an Assert class
or preferrably an `InstanceOfAssertFactory` as shown in the following examples.

> `singleElement`는 iterable에 요소가 하나만 있는지 확인하고 탐색합니다.
> 탐색 후에는 아래 예제와 같이 Assert 클래스 또는 `InstanceOfAssertFactory`를 지정하지 않는 한 객체에 대한 assertion만 사용할 수 있습니다.

Examples:

``` java
Iterable<String> babySimpsons = list("Maggie");

// only object assertions available
assertThat(babySimpsons).singleElement()
                        .isEqualTo("Maggie");

// to get specific typed assertions, pass the corresponding InstanceOfAssertFactory from
// org.assertj.core.api.InstanceOfAssertFactories.STRING), as() is just synthetic sugar for readability
assertThat(babySimpsons).singleElement(as(STRING))
                        .endsWith("gie"); // "gie"로 끝나는 String인지 확인

// alternative for strongly typed assertions
assertThat(babySimpsons, StringAssert.class).singleElement()
                                            .startsWith("Mag"); // "Mag"로 시작하는 String인지 확인
```

<br/>

### Filtering elements

Filtering is handy to target assertions on some specific elements, the filter criteria can be expressed by:

> 필터링은 일부 특정 요소에 대한 assertion을 편리하게 해주며 기준은 다음과 같이 표현할 수 있습니다.

- a java Predicate
- an element property/field having a specific value (or not) or in a set of values (or not)
- an element property/field having a null value
- an element matching some assertions
- an element matching a Condition

> - 자바의 Predicate
> - 특정 값을 갖거나 (갖지 않거나) 일련의 값을 가지는 (갖지 않는) 요소 속성/필드
> - null 값을 갖는 특정 요소 속성/필드
> - 일부 assertion과 일치하는 요소
> - 조건과 일치하는 요소

Let’s explore these options in some examples taken from FilterExamples from the assertions-examples project.

> 이러한 옵션들을 몇 가지 예시와 함께 알아보겠습니다.

**Filtering with a Predicate**

You specify the filter condition using simple predicate, best expressed with a lambda.

> 당신은 predicate를 사용한 람다를 통해 필터 조건을 지정할 수 있습니다.

Example:

``` java
assertThat(fellowshipOfTheRing).filteredOn( character -> character.getName().contains("o") ) // 람다 사용
                               .containsOnly(aragorn, frodo, legolas, boromir);
```

**Filtering on a property or a field**

First you specify the property/field name to filter on and then its expected value.
The filter first tries to get the value from a property, then from a field.
Reading private fields is supported by default,
but can be disabled globally by calling `Assertions.setAllowExtractingPrivateFields(false)`.

> 먼저 필터링할 속성/필드 이름을 지정한 다음에 그것의 예상 값을 지정합니다.
> 필터는 먼저 속성에서 값을 가져온 다음에 필드에서 값을 가져오려고 시도합니다.
> 기본적으로 private 필드를 읽어들이는 것은 지원하는 것으로 설정되어 있으나,
> `Assertions.setAllowExtractingPrivateFields(false)`을 통해 전역적으로 비활성화할 수 있습니다.

Filter supports nested properties/fields.
Note that if an intermediate value is null the whole nested property/field is considered to be null,
for example reading `"address.street.name"` will return null if `"address.street"` is null.

> Filter는 중첩된 속성/필드를 지원합니다.
> 중간 값이 null이면 중첩된 속성/필드 전체가 null로 간주됩니다.
> 예를 들어 `"address.street.name"`을 읽을 때 `"address.street"`가 null이면 null이 반환됩니다.

Filters support these basic operations : `not`, `in`, `notIn`

> 필터는 다음과 같은 기본 작업을 지원합니다 : `not`, `in`, `notIn`

``` java
import static org.assertj.core.api.Assertions.in;
import static org.assertj.core.api.Assertions.not;
import static org.assertj.core.api.Assertions.notIn;
...

// filters use introspection to get property/field values
// 필터는 내부 검사를 사용하여 속성/필드 값을 가져옵니다.
assertThat(fellowshipOfTheRing).filteredOn("race", HOBBIT)
                               .containsOnly(sam, frodo, pippin, merry);

// nested properties are supported
// `"race.name"` 과 같은 중첩된 속성을 지원합니다.
assertThat(fellowshipOfTheRing).filteredOn("race.name", "Man")
                               .containsOnly(aragorn, boromir);

// you can apply different comparison
// `not`, `in`, `notIn`을 사용하여 조건을 다르게 변경할 수 있습니다.
assertThat(fellowshipOfTheRing).filteredOn("race", notIn(HOBBIT, MAN))
                               .containsOnly(gandalf, gimli, legolas);

assertThat(fellowshipOfTheRing).filteredOn("race", in(MAIA, MAN))
                               .containsOnly(gandalf, boromir, aragorn);

assertThat(fellowshipOfTheRing).filteredOn("race", not(HOBBIT))
                               .containsOnly(gandalf, boromir, aragorn, gimli, legolas);

// you can chain multiple filter criteria
// 여러 개의 필터 기준을 체이닝할 수 있습니다.
assertThat(fellowshipOfTheRing).filteredOn("race", MAN)
                               .filteredOn("name", not("Boromir"))
                               .containsOnly(aragorn);
```

**Filtering on a function return value**

This is a more flexible way of getting the value to filter on but note
that there is no support for operators like `not`, `in` and `notIn`.

``` java
assertThat(fellowshipOfTheRing).filteredOn(TolkienCharacter::getRace, HOBBIT)
                               .containsOnly(sam, frodo, pippin, merry);
```

**Filtering on null value**

Filters the elements whose specified property/field is null.

Filter supports nested properties/fields.
Note that if an intermediate value is null the whole nested property/field is considered to be null,
for example reading `"address.street.name"` will return null if `"address.street"` is null.

``` java
TolkienCharacter pippin = new TolkienCharacter("Pippin", 28, HOBBIT);
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, HOBBIT);
TolkienCharacter merry = new TolkienCharacter("Merry", 36, HOBBIT);
TolkienCharacter mysteriousHobbit = new TolkienCharacter(null, 38, HOBBIT);

List<TolkienCharacter> hobbits = list(frodo, mysteriousHobbit, merry, pippin);

assertThat(hobbits).filteredOnNull("name"))
                   .singleElement()
                   .isEqualTo(mysteriousHobbit);
```

**Filtering elements matchin given assertions**

Filters the iterable under test keeping only elements matching
the given assertions specified with a `Consumer`.

Example: check hobbits whose age < 34

``` java
TolkienCharacter pippin = new TolkienCharacter("Pippin", 28, HOBBIT);
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, HOBBIT);
TolkienCharacter merry = new TolkienCharacter("Merry", 36, HOBBIT);
TolkienCharacter sam = new TolkienCharacter("Sam", 38, HOBBIT);

List<TolkienCharacter> hobbits = list(frodo, sam, merry, pippin);

assertThat(hobbits).filteredOnAssertions(hobbit -> assertThat(hobbit.age).isLessThan(34))
                   .containsOnly(frodo, pippin);
```

**Filtering with a Condition**

Filter the iterable/array under test keeping only elements matching the given `Condition`.

Two methods are available : being(Condition) and having(Condition).
They do the same job - pick the one that makes your code more readable!

``` java
import org.assertj.core.api.Condition;

Condition<Player> mvpStats= new Condition<Player>(player -> {
return player.pointsPerGame() > 20 && (player.assistsPerGame() >= 8 || player.reboundsPerGame() >= 8);
}, "mvp");

List<Player> players;
players.add(rose); // Derrick Rose : 25 ppg - 8 assists - 5 rebounds
players.add(lebron); // Lebron James : 27 ppg - 6 assists - 9 rebounds
players.add(noah); // Joachim Noah : 8 ppg - 5 assists - 11 rebounds

// noah does not have more than 20 ppg
assertThat(players).filteredOn(mvpStats)
                   .containsOnly(rose, lebron);
```
