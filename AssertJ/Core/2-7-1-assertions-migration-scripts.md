## Migrating assertions

This page will help you converting your existing JUnit assertions to AssertJ ones. Note that both types of assertions can coexist, you don’t have to migrate all at once.

> 이번 장은 기존의 JUnit 검증문을 AssertJ 검증문으로 변환하는 법에 대해 설명합니다. 두 유형의 검증문은 공존할 수 있으므로 한 번에 모두 마이그레이션할 필요는 없습니다.

The idea is to convert code like :

> 다음과 같은 코드를:

```
assertEquals(expected, actual); // Junit Assertion
```

to:

> 아래와 같이 변경하는 것입니다:

```
assertThat(actual).isEqualTo(expected); // AssertJ Assertion
```

## 2.7.1. Assertions migration scripts

It is as simple as running one of the following script depending on which test framework you are using:

> 사용 중인 테스트 프레임워크에 따라 다음 스크립트 중 하나를 실행하기만 하면 됩니다.

- JUnit 3/4 to AssertJ migration script : JUnit 3/4용

- JUnit 5 to AssertJ migration script : JUnit 5용

- TestNG to AssertJ migration script : TestNG용

Each shell scripts is based on the sed stream editor and regexps. It recursively looks at all *Test.java files and performs search and replace to convert assertions to AssertJ ones.

> 각 쉘 스크립트는 sed 스트림 에디터와 정규식을 기반으로 합니다.
> 이 스크립트는 재귀적으로 모든 `*Test.java` 파일을 살펴보고 검색과 치환 작업을 수행하여 assertion들을 AssertJ assertions로 변환합니다.

The script handles the cases where you use an assertion description, for example:

> 스크립트는 다음과 같이 assertion 설명을 사용하는 경우도 처리합니다:

```
assertEquals("test context", "a", "a");
```

will be replaced by:

> 위와 같이 작성된 assertion은 다음과 같이 변환됩니다:

```
assertThat("a").as("test context").isEqualTo("a");
```

Note that the script does a best effort and some assertions might not be converted if formatted on multiple lines. Anyway the script usually migrates the vast majority of assertions.

> 스크립트가 최선을 다하겠지만 일부 변환이 제대로 이루어지지 않을 수 있습니다.
> 그래도 대부분의 검증문을 성공적으로 마이그레이션합니다.

The script works on windows within a bash console like git bash (tested a long time ago) or cygwin (not tested).

> 스크립트는 windows의 git bash나(예전 버전에서 테스트됨) cygwin(테스트하지 않음)과 같은 bash 콘솔에서도 작동할 것입니다.

### Usage

Execute the script in the base directory containing the test files:

> 테스트 파일을 포함하는 베이스 디렉토리에서 스트립트를 실행하세요:

```
cd ./src/test/java
./convert-junit-assertions-to-assertj.sh
```

If the `*Test.java` file pattern does not suit you, just specify another as an argument:

> 만약 `*Test.java` 파일 패턴이 적합하지 않으면 다른 것을 인수로 지정하세요:

```
# enclose your pattern with double quotes "" to avoid it to be expanded by your shell prematurely
./convert-junit-assertions-to-assertj.sh "*IT.java"
```

After executing it, you will need to :

> 실행한 뒤에 다음과 같은 것들을 해야합니다:

- Optimize imports with your IDE to remove unused imports.

> - 사용하지 않는 import문들을 제거하고 최적화하세요

- If you were using `assertEquals` with a delta to compare numbers, you will need to statically import `org.assertj.core.api.Assertions.within` which is how you express deltas in AssertJ (see the number_assertions_with_offset_examples() test at the end of `NumberAssertionsExamples`).

> - 만약 delta를 사용하여 숫자를 비교할 때 assertEquals를 사용한다면, AssertJ에서 delta를 표현하는 방법인 org.assertj.core.api.Assertions.within을 정적으로 가져와야 합니다. (NumberAssertionsExamples의 끝에 있는 number_assertions_with_offset_examples() 테스트를 참고하세요.)

> 여기서 "delta"는 숫자 비교에서 허용 가능한 오차 범위를 의미합니다. 예를 들어 두 실수 값 A와 B를 비교할 때, A와 B의 차이가 특정한 오차 범위 내에 있는지 확인하고 싶다면, delta를 사용하여 두 값의 차이가 허용 가능한 오차 범위 내에 있는지 검사할 수 있습니다.

### Script output

**스크립트 결과 출력 예시**

```
Converting JUnit assertions to AssertJ assertions on files matching pattern : *Test.java

 1 - Replacing : assertEquals(0, myList.size()) ............... by : assertThat(myList).isEmpty()
 2 - Replacing : assertEquals(expectedSize, myList.size()) .... by : assertThat(myList).hasSize(expectedSize)
 3 - Replacing : assertEquals(expectedDouble, actual, delta) .. by : assertThat(actual).isCloseTo(expectedDouble, within(delta))
 4 - Replacing : assertEquals(expected, actual) ............... by : assertThat(actual).isEqualTo(expected)
 5 - Replacing : assertArrayEquals(expectedArray, actual) ..... by : assertThat(actual).isEqualTo(expectedArray)
 6 - Replacing : assertNull(actual) ........................... by : assertThat(actual).isNull()
 7 - Replacing : assertNotNull(actual) ........................ by : assertThat(actual).isNotNull()
 8 - Replacing : assertTrue(logicalCondition) ................. by : assertThat(logicalCondition).isTrue()
 9 - Replacing : assertFalse(logicalCondition) ................ by : assertThat(logicalCondition).isFalse()
10 - Replacing : assertSame(expected, actual) ................. by : assertThat(actual).isSameAs(expected)
11 - Replacing : assertNotSame(expected, actual) .............. by : assertThat(actual).isNotSameAs(expected)

Replacing JUnit static imports by AssertJ ones, at this point you will probably need to :
12 --- optimize imports with your IDE to remove unused imports
12 --- add "import static org.assertj.core.api.Assertions.within;" if you were using JUnit number assertions with deltas
```
