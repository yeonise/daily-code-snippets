## 2.1 What is AssertJ Core?

**AssertJ Core란?**

AssertJ is a Java library that provides a rich set of assertions and
truly helpful error messages, improves test code readability,
and is designed to be super easy to use within your favorite IDE.

> AssertJ는 Java 라이브러리로, 다양한 조건과 유용한 에러 메시지를 제공하고
> 테스트 코드의 가독성을 높이며 여러분이 좋아하는 IDE 내에서 매우 쉽게 사용하도록 설계되었습니다.

<br/>

http://www.javadoc.io/doc/org.assertj/assertj-core/ is the latest version of assertj core javadoc,
each assertion is explained, most of them with code examples so be sure to check it
if you want to know what a specific assertion does.

> http://www.javadoc.io/doc/org.assertj/assertj-core/는 assertJ core에 대한 최신 Java 문서입니다.
> 각각의 assertion에 대해 설명되어 있으며 대부분 코드 예제가 포함되어 있습니다.
> 특정 assertion이 무엇을 하는지 알고 싶은 경우 참고하세요.

<br/>

Here are a few examples of AssertJ assertions:

> AssertJ assertions에 대한 몇 가지 예시:

``` java
// entry point for all assertThat methods and utility methods (e.g. entry)
// 모든 assertThat 메소드 및 유틸리티 메소드를 사용하기 위해 import합니다.
import static org.assertj.core.api.Assertions.*;

// basic assertions
// 일치하는 경우와 일치하지 않는 경우를 검사합니다.
assertThat(frodo.getName()).isEqualTo("Frodo");
assertThat(frodo).isNotEqualTo(sauron);

// chaining string specific assertions
// chaining을 사용하여 조금 더 구체적으로 검사할 수 있습니다.
assertThat(frodo.getName()).startsWith("Fro") // 시작 조건
                           .endsWith("do") // 끝 조건
                           .isEqualToIgnoringCase("frodo"); // 대소문자 구분 없이 일치하는지 검사

// collection specific assertions (there are plenty more)
// in the examples below fellowshipOfTheRing is a List<TolkienCharacter>
assertThat(fellowshipOfTheRing).hasSize(9)
                               .contains(frodo, sam)
                               .doesNotContain(sauron);

// as() is used to describe the test and will be shown before the error message
// as()는 테스트를 설명하는 데 사용되며 오류 메시지 앞에 표시됩니다.
assertThat(frodo.getAge()).as("check %s's age", frodo.getName()).isEqualTo(33);

// exception assertion, standard style ...
// 예외 검사의 표준 사용 방법입니다.
assertThatThrownBy(() -> { throw new Exception("boom!"); }).hasMessage("boom!");
// ... or BDD style
Throwable thrown = catchThrowable(() -> { throw new Exception("boom!"); });
assertThat(thrown).hasMessageContaining("boom");

// using the 'extracting' feature to check fellowshipOfTheRing character's names
assertThat(fellowshipOfTheRing).extracting(TolkienCharacter::getName)
                               .doesNotContain("Sauron", "Elrond");

// extracting multiple values at once grouped in tuples
// 튜플로 그룹화된 여러 값을 한 번에 추출합니다.
assertThat(fellowshipOfTheRing).extracting("name", "age", "race.name")
                               .contains(tuple("Boromir", 37, "Man"),
                                         tuple("Sam", 38, "Hobbit"),
                                         tuple("Legolas", 1000, "Elf"));

// filtering a collection before asserting
// 검사 전에 "o"를 포함한 이름들만 필터링합니다.
assertThat(fellowshipOfTheRing).filteredOn(character -> character.getName().contains("o"))
                               .containsOnly(aragorn, frodo, legolas, boromir);

// combining filtering and extraction (yes we can)
// 필터링과 추출을 같이 사용할 수도 있습니다.
assertThat(fellowshipOfTheRing).filteredOn(character -> character.getName().contains("o"))
                               .containsOnly(aragorn, frodo, legolas, boromir)
                               .extracting(character -> character.getRace().getName())
                               .contains("Hobbit", "Elf", "Man");

// and many more assertions: iterable, stream, array, map, dates, path, file, numbers, predicate, optional ...
```
