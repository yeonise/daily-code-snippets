## 2.5.3. Assertion Description

It is often valuable to describe the assertion performed,
especially for boolean assertions where the default error message just complains that it got false instead of true (or vice versa).

> 기본 오류 메시지가 참 또는 거짓을 반환하는 boolean assertion인 경우, 어떤 assertion을 수행했는지 설명하는 것이 좋습니다.

<br/>

You can set such a description with as(String description, Object... args)
but remember to do it before calling the assertion otherwise it is simply ignored as a failing assertion breaks the chained calls.

> `as(String description, Obje... args)`로 설명을 설정할 수 있습니다. 단, assertion을 호출하기 전에 먼저 설정해야 합니다. 그렇지 않으면 연결된 그 뒤로 연결된 호출을 무시하며 중단됩니다.

<br/>

Example of a failing assertion with a description:

> 실패한 예시:

``` java
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);

// failing assertion, remember to call as() before the assertion!
// 반드시 assertion 사용 전에 as()를 호출해야 합니다!
assertThat(frodo.getAge()).as("check %s's age", frodo.getName())
                          .isEqualTo(100);
```

The error message starts with the given description in `[]` :

> 에러 메시지는 `[]` 안의 설명으로 시작합니다 :

``` text
[check Frodo's age] expected:<100> but was:<33>
// 예상 결과 : <100> 수행 결과 : <33>
```

<br/>

### Printing or consuming description

AssertJ can print each assertion description (when it is set), to do so call `Assertions.setPrintAssertionsDescription(true);`.

If printing assertion descriptions is not what you need, you can alternatively register a `Consumer<Description>` that will be called each time a description is set.

Both options are exposed in AssertJ `Configuration` class.

> AssertJ는 `Assertions.setPrintAssertionsDescription(true);`을 호출하여 설명을 출력할 수 있습니다.
> 만약 출력 형식이 당신이 필요로 하는 것과 다르다면, `Consumer<Description>`을 사용하여 직접 설명 형식을 설정할 수 있습니다.
> 두 가지 옵션 모두 AssertJ의 `Configuration` 클래스와 연관됩니다.

<br/>

Example: using a description consumer

``` java
// initialize the description consumer
final StringBuilder descriptionReportBuilder = new StringBuilder(String.format("Assertions:%n")); // %n : 줄 바꾸기
Consumer<Description> descriptionConsumer = desc -> descriptionReportBuilder.append(String.format("-- %s%n", desc)); // %s: desc, %n: 줄 바꾸기

// use the description consumer for any following assertions descriptions.
// 이후 assertions에 대한 설명 설정 시, 위의 consumer를 사용하도록 설정
Assertions.setDescriptionConsumer(descriptionConsumer);

// execute some assertions
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);
assertThat(frodo.getName()).as("check name") // 설명 : "check name"
                          .isEqualTo("Frodo");
assertThat(frodo.getAge()).as("check age") // 설명 : "check age"
                          .isEqualTo(33);

// get the report
String descriptionReport = descriptionReportBuilder.toString(); // builder에 저장된 수행 log를 받아옵니다.
```

resulting descriptionReport:

``` text
Assertions:
-- check name
-- check age
```
