## 2.5.6. Configuring AssertJ

**AssertJ 구성 설정하기**

This section describes the different ways to configure AssertJ,
either by setting configuration properties individually or globally using the `Configuration` class.

> 이번에는 속성을 개별적으로 설정하거나, `Configuration` 클래스를 통해 전역적으로 설정하는 등 AssertJ를 구성하는 다양한 방법에 대해 설명합니다.

To be effective the configuration changes must be applied before the tests are executed,
depending on the scope of the tests this means different things:

> 효과적으로 설정을 변경하기 위해서는 반드시 테스트가 실행되기 전에 적용해야 합니다.
> 테스트 범위에 따라 의미가 달라질 수 있습니다:

- For a single test: change the configuration in the test
and revert it in the `@AfterEach` method (JUnit 5).
- For all tests in a class: change the configuration in the `@BeforeAll` method
and revert the changes in the `@AfterAll` method (JUnit 5).
- To change the configuration before any tests, you can use these options:
  - write a JUnit 5 extension implementing `BeforeAllCallback`.
  - register your own `Configuration` subclass and let AssertJ discover it automagically.

> - 단일 테스트의 경우: 테스트에서 설정을 변경하고, `@AfterEach` 메서드에서 되돌립니다.
> - 클래스 내 모든 테스트: `@BeforeAll` 메서드에서 구성을 변경하고 `@AfterAll` 메서드에서 변경 사항을 되돌립니다.
> - 테스트 전에 구성 및 설정을 변경하고 싶다면 다음과 같은 옵션을 설정할 수 있습니다:
>   - `BeforeAllCallback`을 구현한 Unit 5 확장프로그램을 사용하세요.
>   - `Configuration`의 하위 클래스를 등록한 뒤, AssertJ가 자동으로 찾아서 적용하도록 합니다.

<br/>

### Configuring single properties

**단일 속성 구성하기**

The `Assertions` class provides static methods to change each configuration properties.

> `Assertions` 클래스는 각각의 구성 설정을 변경할 수 있는 static 메서드를 제공합니다.

``` java
Assertions.setAllowComparingPrivateFields(true);
Assertions.setAllowExtractingPrivateFields(false);
Assertions.setExtractBareNamePropertyMethods(false);
Assertions.setLenientDateParsing(true);
Assertions.setMaxElementsForPrinting(100);
Assertions.setMaxLengthForSingleLineDescription(250);
Assertions.setRemoveAssertJRelatedElementsFromStackTrace(true);
Assertions.useRepresentation(myRepresentation);
Assertions.registerCustomDateFormat(myCustomDateFormat);
Assertions.setPrintAssertionsDescription(true);
Assertions.setConsumerDescription(description -> writeToFile(description, report));
```

<br/>

### Representation

**Assertions.useRepresentation(myRepresentation);**

This property allows you to register a `Representation` to control the way AssertJ formats the different types
displayed in the assertion error messages. Consult the Controlling type formatting chapter for details.

> 이 속성을 사용하면, AssertJ의 오류 메시지에 표시되는 다양한 유형의 형식을 지정하는 방식을 제어하는 `Representation`을 등록할 수 있습니다.
> 자세한 내용은 Controlling type formatting 챕터를 참고하세요.

Defaults to `StandardRepresentation`.

> 기본값은 `StandardRepresentation`입니다.

### AllowComparingPrivateFields

**Assertions.setAllowComparingPrivateFields(true);**

Globally sets whether the use of private fields is allowed for field/property by field/property comparison. Defaults to true.

> 필드 또는 속성 비교를 위한 private 필드 사용 여부를 전역적으로 설정합니다.
> 기본값은 true입니다.

### AllowExtractingPrivateFields

**Assertions.setAllowExtractingPrivateFields(false);**

Globally sets whether the AssertJ extracting capability should be allowed to extract private fields. Defaults to true.

> private 필드를 추출 허용 여부를 전역적으로 설정합니다.
> 기본값은 true입니다.

### ExtractBareNamePropertyMethods

**Assertions.setExtractBareNamePropertyMethods(false);**

Globally sets whether the AssertJ `extracting` capability considers bare-named property methods like String name(). Defaults to true.

> AssertJ `extracting`을 사용한 private 필드 추출 허용 여부를 전역적으로 설정합니다.
> 기본값은 true입니다.

> `extracting`을 사용하면 객체의 속성 값을 한 번에 검증할 수 있다고 해요.
> ``` java
> Person person = new Person("John", 30);
> assertThat(person).extracting(Person::getName, Person::getAge).containsExactly("John", 30);
> ```
> `extracting`에 여러 개의 람다식을 전달하면 추출된 값들이 Tuple 형태로 반환됩니다.  
> 그리고 `containsExactly`를 사용하여 name과 age 속성의 값이 순서에 따라 정확히 일치하는지 검증합니다.  
> `containsExactlyInAnyOrder`를 사용하면 순서와 상관없이 정확한 값들이 포함되어 있는지 검증할 수 있습니다.

### LenientDateParsing

**Assertions.setLenientDateParsing(true);**

Specify whether or not date/time parsing is to be lenient for AssertJ default date formats. With lenient parsing, the parser may use heuristics to interpret inputs that do not precisely match this object’s format. With strict parsing, inputs must match this object’s format.

> AssertJ에서 사용하는 기본적인 날짜 형식에 대해서 파싱 시 유연하게 처리할 것인지, 엄격하게 처리할 것인지를 설정하는 옵션입니다.
> 유연한 구문 분석을 사용하면 파서는 휴리스틱을 사용하여 이 개체의 형식과 정확히 일치하지 않는 입력을 추측하여 해석할 수 있습니다.
> 엄격한 구문 분석을 사용하면 입력이 형식과 완전히 일치해야 합니다.

### Custom DateFormat

**Assertions.registerCustomDateFormat(myCustomDateFormat);**

In addition to the default date formats, you can register some custom ones
that AssertJ will use in date assertions (see also `Assertions.registerCustomDateFormat`).

Note that custom date formats take precedence over default ones.

> 기본 날짜 형식 외에도 AssertJ의 날짜 관련 assertion에서 사용할 사용자 지정 형식을 등록할 수 있습니다.  
> 사용자 지정 날짜 형식은 기본 날짜 형식보다 우선됩니다.

### MaxElementsForPrinting

**Assertions.setMaxElementsForPrinting(100);**

In error messages, sets the threshold for how many elements from one iterable/array/map will be included in the in the description.
Defaults to 1000.

The point of this property is to avoid printing iterable/array/map with too many elements in error messages.

> 오류 메시지에서 하나의 배열 또는 맵의 얼마나 많은 요소가 설명에 포함될지에 대한 임계값을 설정합니다.  
> 기본값은 1000입니다.  
> 이 속성의 포인트는 오류 메시지에 너무 많은 요소를 인쇄하지 않도록 하는 것입니다.

### MaxLengthForSingleLineDescription

**Assertions.setMaxLengthForSingleLineDescription(250);**

In error messages, sets the threshold when iterable/array formatting will be on one line
(if their String description is less than this parameter) or it will be formatted with one element per line. Defaults to 80.

> 오류 메시지에서 한 줄에 표시되는 배열의 임계값을 설정합니다.
> 기본값은 80입니다.

Example:

``` java
String[] greatBooks = array("A Game of Thrones", "The Lord of the Rings", "Assassin's Apprentice");
```

this array is formatted on one line as its length < 80:

> 이 배열은 길이가 80보다 작으므로 한 줄에 모두 표시됩니다.

``` java
["A Game of Thrones", "The Lord of the Rings", "Assassin's Apprentice"]
```

Whereas this array ...

> 반면 이 배열은 ...

``` java
String[] greatBooks = array("A Game of Thrones", "The Lord of the Rings", "Assassin's Apprentice", "Guards! Guards! (Discworld)");
```

... is formatted on multiple lines with one element per line:

> 한 줄에 하나씩, 여러 줄로 형식이 지정됩니다.

``` java
["A Game of Thrones",
"The Lord of the Rings",
"Assassin's Apprentice",
"Guards! Guards! (Discworld)"]
```

### RemoveAssertJRelatedElementsFromStackTrace

Sets whether the elements related to AssertJ are removed from assertion errors stack trace. Defaults to true.

> AssertJ와 관련된 오류를 stack trace에서 제거할지 설정합니다.
> 기본값은 true입니다.

### AssertJ Configuration

Since 3.13.0, AssertJ exposes a `org.assertj.core.configuration.Configuration` object
providing access to all AssertJ globally configurable properties.

> 3.13.0부터 AssertJ는 전역적으로 구성 가능한 속성에 대한 접근을 제공하는
> `org.assertj.core.configuration.Configuration`를 제공합니다.

You can create an instance of `org.assertj.core.configuration.Configuration`
and change individual properties through setters or create your own custom configuration
by inheriting from it and overriding the methods
to change the default behavior as in the `CustomConfiguration` example below.

> 여러분은 `org.assertj.core.configuration.Configuration`의 인스턴스를 생성할 수 있으며,  
> setter를 사용하여 각각의 속성을 변경할 수 있습니다.  
> 또한 아래의 예시처럼 configuration을 상속받는 커스텀 설정 클래스를 만들고 메서드 오버라이딩을 통해 기본 동작을 변경할 수 있습니다.

Your configuration will be effective once you call
`Configuration.apply()` or `Configuration.applyAndDisplay()`.

> `Configuration.apply()` 또는 `Configuration.applyAndDisplay()`를 호출하면 구성 설정이 적용됩니다.

Example:

``` java
Configuration configuration = new Configuration();

configuration.setBareNamePropertyExtraction(false);
configuration.setComparingPrivateFields(false);
configuration.setExtractingPrivateFields(false);
configuration.setLenientDateParsing(true);
configuration.setMaxElementsForPrinting(1001);
configuration.setMaxLengthForSingleLineDescription(81);
configuration.setRemoveAssertJRelatedElementsFromStackTrace(false);

// don't forget to apply it!
// 설정을 적용하는 것을 잊지마세요!
configuration.applyAndDisplay();
```

Printing the above configuration produces the following output:

> 위의 설정을 출력하면 아래와 같은 결과가 나옵니다:

``` java
Applying configuration org.assertj.core.configuration.Configuration
- representation .................................. = BinaryRepresentation
- comparingPrivateFieldsEnabled ................... = false
- extractingPrivateFieldsEnabled .................. = true
- bareNamePropertyExtractionEnabled ............... = false
- lenientDateParsingEnabled ....................... = true
- additionnal date formats ........................ = [yyyy_MM_dd, yyyy|MM|dd]
- maxLengthForSingleLineDescription ............... = 150
- maxElementsForPrinting .......................... = 2000
- removeAssertJRelatedElementsFromStackTraceEnabled = true
```

### Automagic configuration discovery

**자동화된 구성 검색**

This section describes a way to register an AssertJ `Configuration`
without using any test framework hooks like `BeforeAllCallback`.

> 이 섹션에서는 `BeforeAllCallback`과 같은 테스트 프레임워크에서 제공하는 콜백 메서드를 사용하지 않고 AssertJ 구성을 등록하는 방법을 설명합니다.

Follow the steps below to register your `Configuration` as an SPI:

> 아래 단계에 따라 구성을 SPI로 등록하세요:

- Create your own configuration inheriting from `org.assertj.core.configuration.Configuration`
- Create a file named `org.assertj.core.configuration.Configuration` in a `META-INF/services` directory
- Make sure `META-INF/services/` is in the runtime classpath, usually putting it in s`rc/test/resources` will do.
- Put the fully qualified class name of your `Configuration` in `services/org.assertj.core.configuration.Configuration`.

> - `org.assertj.core.configuration.Configuration`를 상속하는 구성을 만듭니다.
> - `META-INF/services`에 `org.assertj.core.configuration.Configuration`이라는 파일을 만듭니다.
> - `META-INF/services/`가 런타임 클래스 경로에 있는지 확인하십시오. 일반적으로 `src/test/resources`에 넣으면 됩니다.
> - `services/org.assertj.core.configuration.Configuration`에 구성의 클래스 이름을 입력합니다.

This is all you have to do, AssertJ will pick up the Configuration automatically and display it at the first interaction with AssertJ.

> 이것이 여러분이 해야 할 전부입니다. AssertJ는 자동으로 구성을 선택하고 AssertJ와의 첫 번째 상호 작용에서 이를 표시합니다.

Here’s an example of a custom configuration class:

> 다음은 사용자 지정 구성 클래스의 예입니다:

``` java
package example.core;

import static org.assertj.core.presentation.BinaryRepresentation.BINARY_REPRESENTATION;
import static org.assertj.core.util.Lists.list;

import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.List;

import org.assertj.core.configuration.Configuration;
import org.assertj.core.presentation.Representation;

class CustomConfiguration extends Configuration { // Configuration을 상속

    private static final SimpleDateFormat DATE_FORMAT1 = new SimpleDateFormat("yyyy_MM_dd");
    private static final SimpleDateFormat DATE_FORMAT2 = new SimpleDateFormat("yyyy|MM|dd");
    
    // we keep the default behavior for extractingPrivateFieldsEnabled since it is not overridden
    // 재정의되지 않았기 때문에 extractingPrivateFieldsEnabled에 대한 기본 동작을 유지합니다.
    
    @Override
    public Representation representation() {
    return BINARY_REPRESENTATION;
    }
    
    @Override
    public boolean bareNamePropertyExtractionEnabled() {
    return false;
    }
    
    @Override
    public boolean comparingPrivateFieldsEnabled() {
    return false;
    }
    
    @Override
    public boolean lenientDateParsingEnabled() {
    return true;
    }
    
    @Override
    public List<DateFormat> additionalDateFormats() {
    return list(DATE_FORMAT1, DATE_FORMAT2);
    }
    
    @Override
    public int maxElementsForPrinting() {
    return 2000;
    }
    
    @Override
    public int maxLengthForSingleLineDescription() {
    return 150;
    }
}
```

With this custom configuration,
the content of `META-INF/services/org.assertj.core.configuration.Configuration` must be:

> 이 사용자 정의 구성에서 `META-INF/services/org.assertj.core.configuration.Configuration`의 내용은 다음과 같아야 합니다.

``` java
example.core.CustomConfiguration
```

Printing the `CustomConfiguration` shows:

> `CustomConfiguration`을 출력하면 다음과 같습니다:

``` java
Applying configuration example.core.CustomConfiguration
- representation .................................. = BinaryRepresentation
- comparingPrivateFieldsEnabled ................... = false
- extractingPrivateFieldsEnabled .................. = true
- bareNamePropertyExtractionEnabled ............... = false
- lenientDateParsingEnabled ....................... = true
- additionnal date formats ........................ = [yyyy_MM_dd, yyyy|MM|dd]
- maxLengthForSingleLineDescription ............... = 150
- maxElementsForPrinting .......................... = 2000
- removeAssertJRelatedElementsFromStackTraceEnabled = true
```
