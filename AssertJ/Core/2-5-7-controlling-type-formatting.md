## 2.5.7. Controlling type formatting

Assertions error messages use a `Representation` to format the different types involved.
There are multiple ways of registering a custom `Representation` for assertions:

> assertion의 에러 메세지의 형식은 타입마다 관련된 `Representation`을 참고하여 작성됩니다.
> 사용자 `Representation`을 등록하는 다양한 방법이 있습니다:

- Changing the default global `Representation` by calling Assertions.useRepresentation(myRepresentation) - see Changing the default global scope custom representation
- Changing the `Representation` per assertion with `assertThat(actual).withRepresentation(myRepresentation)` - see Per assertion scope custom representation
- Globally registering a `Configuration` that specifies the `Representation` to use - see AssertJ global configuration.
- Registering multiple fine grained representations that only defines representation of their custom types.

> - `Assertions.useRepresentation(myRepresentation)`을 호출함으써 `Representation`의 전역 설정 값을 변경할 수 있습니다. - Changing the default global scope custom representation을 참고하세요.
> - `assertThat(actual).withRepresentation(myRepresentation)`을 사용해서 assertion마다 `Representation`을 변경할 수 있습니다. - Per assertion scope custom representation을 참고하세요.
> - 사용할 `Representation`을 설정한 `Configuration`을 전역적으로 등록할 수 있습니다. - AssertJ global configuration을 살펴보세요.
> - 사용자 설정을 정의하는 여러 개의 세분화된 `Representation`을 등록할 수 있습니다.

Let’s go over these different options with a custom `Representation`.

> 커스텀 `Representation`을 사용한 다양한 옵션들을 살펴보겠습니다.

### Creating a custom Representation

**사용자 정의 Representation 만들기**

An example of a custom `Representation`:

> 커스텀 `Representation`의 예시:

``` java
// dummy class
private class Example {}

public class CustomRepresentation extends StandardRepresentation { // 1 )

    // override fallbackToStringOf to handle Example formatting
    @Override
    public String fallbackToStringOf(Object o) { // 2 )
        if (o instanceof Example) return "Example";
        // fallback to default formatting.
        return super.fallbackToStringOf(o);
    }
    
    // override a predefined type formatting : String
    // 미리 정의된 형식 재정의 : 문자열
    @Override
    protected String toStringOf(String str) { // 3 )
        return "$" + str + "$";
    }
}
```

1 ) Extends `org.assertj.core.presentation.StandardRepresentation` to get AssertJ default representation.  
2 ) Override `fallbackToStringOf` and handle your specific types before falling back to the default formatting.  
3 ) Change a predefined type formatting by overriding the `toStringOf` method that takes it as a parameter. 

> 1 ) `org.assertj.core.presentation.StandardRepresentation`을 상속받아 기본 representation을 가져옵니다.  
> 2 ) 기본 형식으로 돌아가기 전에 `fallbackToStringOf`을 재정의하여 특정 유형을 처리합니다.  
> 3 ) 매개 변수를 사용하는 `toStringOf` 메서드를 재정의하여 미리 정의된 형식을 변경합니다.  

Let’s see the above custom representation in action when representing `Example` or `String` instances.  

> `Example` 또는 `String` 인스턴스를 나타낼 때 위의 사용자 지정 표현이 어떻게 작동하는지 살펴보겠습니다.

This assertion fails ...

> 아래의 assertion이 실패하면 ...

``` java
assertThat(new Example()).isNull();
```

...with the following error:

> 아래와 같은 에러 메시지가 나타납니다:

``` java
expected:<[null]> but was:<[Example]> // 위의 2 ) 이 적용된 것을 확인할 수 있습니다.
```

This one fails ...

> 아래의 검증이 실패하면 ...

``` java
// this one fails ...
assertThat("foo").startsWith("bar");
```

...with the following error:

> 다음과 같은 에러 메시지가 나타납니다:

``` java
Expecting:
    <$foo$> // 위의 3 ) 이 적용된 것을 확인할 수 있습니다.
to start with:
    <$bar$>
```

### Changing the default global scope custom representation

You only have to register `CustomRepresentation` once but need to do it before executing any tests,
for the tests executed before that, AssertJ will use the default representation.

> 테스트를 실행하기 전에 `CustomRepresentation`을 등록해야 합니다.
> 등록하기 전에 실행된 테스의 경우 AssertJ의 기본 representation을 사용합니다.

``` java
// to call before executing tests
// 테스트를 시작하기 전에 useRepresentation을 호출하여 등록해 주세요.
Assertions.useRepresentation(new CustomRepresentation());
```

Consider writing a JUnit 5 extension implementing `BeforeAllCallback` to make sure the representation
is set once for all before any test is executed.

> 테스트가 실행되기 전에 Representation이 한 번씩 설정되도록 `BeforeAllCallback`을 구현하는 것도 고려해보세요.

### Per assertion scope custom representation

Follow this approach if you want to use a specific representation for a single assertion only.

> 단일 assertion에서만 특정 representation을 사용하려는 경우 다음과 같은 방법을 따르세요.

Example with the failing assertions used before:

``` java
Representation customRepresentation = new CustomRepresentation();

// this assertion fails ...
assertThat(new Example()).withRepresentation(customRepresentation) // withRepresentation을 사용하여 등록할 수 있습니다
                         .isNull();

assertThat("foo").withRepresentation(customRepresentation)
                 .startsWith("bar");
```

### Registering multiple fine-grained representations

**여러 가지 세분화된 표현 등록하기**

Since 3.22.0 AssertJ allows registering multiple representations (one per jar).  
The typical use case is for different domain-specific libraries
to be able to independently register Representation implementations for their specific domain objects.

> AssertJ 3.22.0부터 여러 개의 representation을 등록할 수 있습니다.
> 일반적인 사용 예시로는 특정 도메인 개체에 대한 표현을 독립적으로 등록할 수 있는 다양한 도메인별 라이브러리가 있습니다.

In case different representations can represent the same type, the one with the highest priority wins.

> 서로 다른 표현이 동일한 유형을 나타내는 경우 우선 순위가 높은 것이 적용됩니다.

Let’s take a concrete example where we have two domain specific libraries:
Lotr and star wars and a project that uses them both.

> 두 개의 도메인별 라이브러리가 있는 것으로 구체적인 예를 살펴보겠습니다.

The Lotr library is composed of an `Hobbit` class and a specific representation for it,
note that `LotrRepresentation` represents Hobbits starting with `HOBBIT` unlike `Hobbit` `toString` method:

> Lotr 라이브러리는 Hobbit 클래스와 그에 대한 특정 표현으로 구성됩니다.
> `LotrRepresentation`은 `Hobbit` 클래스의 `toString`과 달리 `HOBBIT`으로 시작하는 `Hobbits`를 나타냅니다.

``` java
package org.assertj.example.lotr;

public class Hobbit {

    public String name;
    public String age;

    @Override
    public String toString() {
        return format("Hobbit [name=%s, age=%s]", name, age);
    }
}

public class LotrRepresentation implements Representation {

    @Override
    public String toStringOf(Object object) {
        if (object instanceof Hobbit) {
            Hobbit hobbit = (Hobbit) object;
            return String.format("HOBBIT [name=%s, age=%s]", hobbit.name, hobbit.age);
        }
        return null;
    }

    // only needed if another library was to represent Hobbit, in this case the one with highest priority wins
    @Override
    public int getPriority() {
        return 5;
    }
}
```

`LotrRepresentation` is registered by creating a
`META-INF/services/org.assertj.core.presentation.Representation` file
that contain `org.assertj.example.lotr.LotrRepresentation`,
the file must be available in the classpath
(typically by putting in it src/main/resources it will end up in the library jar).

> `LotrRepresentation`은 `org.assertj.example.lotr.LotrRepresentation`을 포함하는
> `META-INF/services/org.assertj.core.presentation.Representation` 파일을 생성하여 등록합니다.
> 파일은 클래스 경로에서 사용할 수 있어야 합니다 (일반적으로 src/main/resources에 넣으면 라이브러리 jar에 저장됩니다).

Similarly the star wars library defines a `Jedi` and a `StarWarsRepresentation`:

> 마찬가지로 스타워즈 라이브러리는 `Jedi`와 `StarWarsRepresentation`을 정의합니다.

``` java
package org.assertj.example.starwars;

public class Jedi {
    
    public String name;
    public String age;
    
    @Override
    public String toString() {
        return format("Jedi [name=%s, age=%s]", name, age);
    }
}
    
public class StarWarsRepresentation implements Representation {
    
    @Override
    public String toStringOf(Object object) {
        if (object instanceof Jedi) {
            Jedi jedi = (Jedi) object;
            return String.format("JEDI [name=%s, age=%s]", jedi.name, jedi.age);
        }
        return null;
    }
    
    @Override
    public int getPriority() {
        return 10;
    }
}
```

Same as the Lotr library, `StarWarsRepresentation` is registered
by creating a `META-INF/services/org.assertj.core.presentation.Representation` file
that contain `org.assertj.example.starwars.StarWarsRepresentation`.

> Lotr 라이브러리와 동일하게 `StarWarsRepresentation`은
> `org.assertj.example.starwars.StarWarsRepresentatio`n을 포함하는
> `META-INF/services/org.assertj.core.presentation.Representation`파일을 생성하여 등록합니다.

The consuming project specifies both libraries as dependencies,
since both have registered a representation, AssertJ will discover them and keep them
in a composite representation that aggregates all registered representaions.

> 사용할 프로젝트에 두 라이브러리를 종속성으로 지정합니다.
> 둘 다 representation을 등록했기 때문에 AssertJ는 라이브러리를 검색하고 등록된 모든 representation을 모으는 복합 representation에 보관합니다.

The following test fails with frodo and luke being represented
by `LotrRepresentation` and `StarWarsRepresentation` respectively.

> 다음 테스트는 각각 `LotrRepresentation` 및 `StarWarsRepresentation`에 설정된 표현대로 출력됩니다.

``` java
Hobbit frodo = new Hobbit();
frodo.name = "Frodo";
frodo.age = "33";

Jedi luke = new Jedi();
luke.name = "Luke";
luke.age = "23";

assertThat(frodo).isEqualTo(luke);
```

Error message:

``` java
org.opentest4j.AssertionFailedError:
expected: JEDI [name=Luke, age=23]
but was: HOBBIT [name=Frodo, age=33]
```
