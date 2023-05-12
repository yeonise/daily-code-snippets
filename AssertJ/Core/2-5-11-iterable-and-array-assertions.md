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

**함수의 반환 값을 필터링**

This is a more flexible way of getting the value to filter on but note
that there is no support for operators like `not`, `in` and `notIn`.

> 이것은 필터링된 값을 얻는 보다 유연한 방법이지만, `not`, `in` 그리고 `notIn`과 같은 연산자를 지원하지 않습니다.

``` java
assertThat(fellowshipOfTheRing).filteredOn(TolkienCharacter::getRace, HOBBIT)
                               .containsOnly(sam, frodo, pippin, merry);
```

**Filtering on null value**

**null 값 필터링**

Filters the elements whose specified property/field is null.

> 속성/필드가 null인 요소를 필터링합니다.

Filter supports nested properties/fields.
Note that if an intermediate value is null the whole nested property/field is considered to be null,
for example reading `"address.street.name"` will return null if `"address.street"` is null.

> 필터는 중첩된 속성/필드를 지원합니다.
> 중간 값이 null인 경우 전체의 속성/필드가 null인 것으로 간주됩니다.
> 예를 들어 `"address.street.name"`을 읽을 때, `"address.street"`이 null이라면 null을 반환할 것입니다.

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

**지정된 assertion과 일치하는 필터링 요소**

Filters the iterable under test keeping only elements matching
the given assertions specified with a `Consumer`.

> 테스트 중인 iterable을 필터링하여 Consumer로 지정된 assertion과 일치하는 요소만 유지합니다.

Example: check hobbits whose age < 34

> 예: hobbit들 중에서 나이가 34살 미만인 경우만 남기기

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

**조건에 맞는 필터링**

Filter the iterable/array under test keeping only elements matching the given `Condition`.

> 주어진 조건과 일치하는 요소만 유지하면서 테스트 중인 iterable/array를 필터링합니다.

Two methods are available : being(Condition) and having(Condition).
They do the same job - pick the one that makes your code more readable!

> 2가지 메서드를 사용할 수 있습니다 : 1. being과 2. having
> 2가지 메서드는 같은 작업을 수행합니다 - 코드를 더 읽기 쉽게 만드는 것을 선택하세요!

``` java
import org.assertj.core.api.Condition;

Condition<Player> mvpStats= new Condition<Player>(player -> {
    // 게임 당 점수가 20점 이상이면서 게임 당 어시스트가 8점 이상이거나 게임 당 리바운드가 8점 이상인 선수만 남겨서 반환합니다.
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

<br/>

### Extracting elements values

What problem extracting solves

Let’s say you have called some service and got a list (or an array) of `TolkienCharacter`,
to check the results you have to build the expected TolkienCharacters, that can be quite tedious!

> 서비스를 호출하여 `TolkienCharacter`의 리스트(또는 배열)을 얻었다고 가정해 보겠습니다.
> 그리고 결과를 확인하려면 예상되는 `TolkienCharacter`를 빌드해야 합니다.
> 상당히 지루한 작업이죠!

``` java
List<TolkienCharacter> fellowshipOfTheRing = tolkienDao.findHeroes();  // frodo, sam, aragorn ...

// requires creation of frodo and aragorn, the expected TolkienCharacters
assertThat(fellowshipOfTheRing).contains(frodo, aragorn);
```

Instead, it is usually enough to check some fields or properties on the elements,
for that you have to extract the fields/properties before performing your assertions, something like:

> 대신, 일반적으로 다음과 같이 assertion을 수행하기 전에 필드/속성을 추출하기 때문에
> 요소의 일부 필드 또는 속성을 확인하는 것으로 충분히 검사할 수 있습니다.

``` java
// extract the names ...
List<String> names = fellowshipOfTheRing.stream().map(TolkienCharacter::getName).collect(toList());
// ... and finally assert something
assertThat(names).contains("Boromir", "Gandalf", "Frodo", "Legolas");
```

This is too much work (even with the stream API), instead AssertJ can help extracting values
from the elements under tests, there are several ways of doing so:

> 이것은 너무 많은 작업을 필요로 합니다. (stream API를 사용하는 경우에도)
> 대신 AssertJ는 테스트 중인 요소에서 값을 추출하는 데 도움이 되는 몇 가지 방법을 제공합니다:

- Extracting a single value per element
- Extracting a multiple values per element
- Extracting and flattening multiple values per element

> - 요소에서 단일 값 추출
> - 요소에서 여러 값 추출
> - 요소에서 여러 값 추출 및 평면화

**Extracting single value per element**

Specify the field/property to extract (or pass a `Function`)
from each elements and perform assertions on the extracted values.

> 각 요소에서 추출(또는 함수 전달)할 필드/속성을 지정하고 추출된 값에 대한 assertion을 수행합니다.

Extracting by name can access private fields/properties
which is handy to check internals not exposed with public methods (lambda won’t work here),
it also supports nested field/property like `"race.name"`.

> 이름으로 추출하면 public 메서드로 노출되지 않은 내부를 확인하는 데 편리한 private 필드/속성에 접근할 수 있으며(여기서 lambda는 작동하지 않음)
> `"race.name"`과 같은 중첩된 필드/속성도 지원합니다.

Examples:

``` java
// "name" needs to be either a property or a field of the TolkienCharacter class
// "name"은 TolkienCharacter 클래스의 속성 또는 필드여야 합니다.
assertThat(fellowshipOfTheRing).extracting("name")
                                .contains("Boromir", "Gandalf", "Frodo", "Legolas") // 포함하는 것
                                .doesNotContain("Sauron", "Elrond"); // 포함하지 않는 것

// specifying nested field/property is supported
// 중첩된 필드/속성 확인을 지원
assertThat(fellowshipOfTheRing).extracting("race.name")
                                .contains("Man", "Maia", "Hobbit", "Elf");

// same thing with a lambda which is type safe and refactoring friendly:
// 람다를 사용하여 리팩토링
assertThat(fellowshipOfTheRing).extracting(TolkienCharacter::getName)
                                .contains("Boromir", "Gandalf", "Frodo", "Legolas");

// same thing map an alias of extracting:
// map을 사용한 예시
assertThat(fellowshipOfTheRing).map(TolkienCharacter::getName)
                                .contains("Boromir", "Gandalf", "Frodo", "Legolas");
```

Note that extracting one property can be made strongly typed by
giving the property type as the second argument.

> 두 번째 인자로 속성의 타입을 지정함으로써 강력한 형식으로 하나의 속성을 추출할 수 있습니다.

``` java
// to have type safe extracting, use the second parameter to pass the expected property type:
// type-safe한 추출을 위해 두번째 인자로 예상하는 타입을 지정할 수 있습니다:
assertThat(fellowshipOfTheRing).extracting("name", String.class) // String type 검증
                                .contains("Boromir", "Gandalf", "Frodo", "Legolas")
                                .doesNotContain("Sauron", "Elrond");
```

**Extracting multiple values**

You can extract several values from the elements under test and check them using tuples.

> 테스트 중인 요소에서 여러 값을 추출하고 튜플을 사용하여 확인할 수 있습니다.

As an example, let’s check the name, age and race’s name of each TolkienCharacter element:

> 예를 들어, 각 TolkienCharacter 요소의 이름, 나이, 인종 이름을 확인해 보겠습니다.

``` java
// when checking several properties/fields you have to use tuples:
// 여러 속성/필드를 확인할 때는 튜플을 사용해야 합니다:
import static org.assertj.core.api.Assertions.tuple;

// extracting name, age and and race.name nested property
assertThat(fellowshipOfTheRing).extracting("name", "age", "race.name")
                                .contains(tuple("Boromir", 37, "Man"),
                                          tuple("Sam", 38, "Hobbit"),
                                          tuple("Legolas", 1000, "Elf"));

// same assertion with functions for type safety:
// 함수를 사용하여 type-safety하게 같은 assertion 수행
assertThat(fellowshipOfTheRing).extracting(TolkienCharacter::getName,
                                    tolkienCharacter -> tolkienCharacter.age,
                                    tolkienCharacter -> tolkienCharacter.getRace().getName())
                                .contains(tuple("Boromir", 37, "Man"),
                                          tuple("Sam", 38, "Hobbit"),
                                          tuple("Legolas", 1000, "Elf"));
```

The extracted name, age and race’s name values of the current element are grouped in a tuple,
thus you need to use tuples for specifying the expected values.

> 추출된 현재 요소의 이름, 나이, 인종 이름 값은 튜플로 그룹화되어 있으므로 튜플을 사용하여 예상 값을 지정해야 합니다.

More examples are available in IterableAssertionsExamples.java of the assertj-examples project.

> 더 많은 예시는 IterableAssertionsExamples.java of the assertj-examples project에서 확인할 수 있습니다.

**Extracting and flattening multiple values per element**

Flat extracting is hard to explain but easy to understand with an example,
so let’s see how it works (in functional programming it is juts a flatMap).

> 플랫 추출은 설명보다 예제를 통해 이해하기 쉬우므로 어떻게 작동하는지 예시를 통해 살펴보겠습니다.(함수형 프로그래밍에서는 플랫맵을 사용합니다)

Let’s assume we have a `Player` class with a `teamMates` property returning a
`List<Player>` and we want to assert that it returns the expected players:

> `List<Player>`를 반환하는 teamMates 속성이 있는 Player 클래스가 있고 예상 플레이어를 반환한다고 주장을 가정해 보겠습니다.

``` java
Player jordan = ... // initialized with Pippen and Kukoc team mates
Player magic = ... // initialized with Jabbar and Worthy team mates
List<Player> reallyGoodPlayers = list(jordan, magic);

// check all team mates by specifying the teamMates property (Player has a getTeamMates() method):
// teamMates 속성을 지정하여 모든 팀원을 확인합니다(플레이어에는 getTeamMates() 메서드가 있음):
assertThat(reallyGoodPlayers).flatExtracting("teamMates")
                             .contains(pippen, kukoc, jabbar, worthy);

// alternatively, you can use a Function for type safety:
// 또는, type-safety를 위해서 함수를 사용할 수도 있습니다:
assertThat(reallyGoodPlayers).flatExtracting(BasketBallPlayer::getTeamMates)
                             .contains(pippen, kukoc, jabbar, worthy);

// flatMap is an alias of flatExtracting:
// flatMap은 flatExtracting의 별칭입니다:
assertThat(reallyGoodPlayers).flatMap(BasketBallPlayer::getTeamMates)
                             .contains(pippen, kukoc, jabbar, worthy);

// if you use extracting instead of flatExtracting the result would be a list of list of players so the assertion becomes:
// flatExtracting 대신 추출을 사용하면 결과는 플레이어 목록이 되므로 assertion은 다음과 같이 됩니다:
assertThat(reallyGoodPlayers).extracting("teamMates")
                             .contains(list(pippen, kukoc), list(jabbar, worthy));
```

You can use `flatMap` in place of `flatExtracting` (except for the variant taking a String)

> flatExtracting 대신 flatMap을 사용할 수 있습니다.(문자열을 사용하는 경우 제외)

Flat extracting can be used to group multiple values if you don’t want to use `extracting` and tuples:

> `extracting`과 튜플을 사용하지 않으려면 플랫 추출을 사용하여 여러 값을 그룹화할 수 있습니다.

``` java
// extract a list of values, flatten them and use contains assertion
// 값들의 리스트를 추출한 다음, contains assertion을 사용하여 평명화합니다.
assertThat(fellowshipOfTheRing).flatExtracting("name", "race.name")
                               .contains("Frodo", "Hobbit", "Legolas", "Elf");

// same assertions with Functions:
// 함수를 사용한 같은 assertion:
assertThat(fellowshipOfTheRing).flatExtracting(TolkienCharacter::getName,
                                            tc -> tc.getRace().getName())
                               .contains("Frodo", "Hobbit", "Legolas", "Elf");
```

<br/>

### Comparing elements with a specific comparator

`usingElementComparator` allows you to change the way elements are compared
(instead of using the elements `equals` method).

> `usingElementComparator`를 사용하여 요소를 비교하는 방식을 변경할 수 있습니다. (`equals` 메서드를 사용하는 대신)

Examples:

``` java
List<TolkienCharacter> fellowshipOfTheRing = list(frodo, sam, merry, pippin, gandald, legolas, boromir, aragorn, gimli);

// the fellowshipOfTheRing includes Gandalf but not Sauron ...
// fellowshipOfTheRing이 Gandalf는 포함하고 Sauron은 포함하지 않을 때 ...
assertThat(fellowshipOfTheRing).contains(gandalf)
                               .doesNotContain(sauron);

// ... but if we compare only races, Sauron is in fellowshipOfTheRing since he's a Maia like Gandalf
// ... 하지만 종족만 비교한다면 Sauron은 Gandalf와 같은 Maia이므로 FellowshipOfTheRing에 있음을 확인할 수 있습니다.
assertThat(fellowshipOfTheRing).usingElementComparator((t1, t2) -> t1.getRace().compareTo(t2.getRace()))
                               .contains(sauron);
```
