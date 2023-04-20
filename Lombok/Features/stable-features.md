# Lombok Features

## val
### Finally! Hassle-free final local variables.
> 마침내! 혼란이 없는 final 지역 변수.

<br>

val was introduced in lombok 0.10.
> `val`은 lombok 0.10.에서 소개됐다.

NEW in Lombok 1.18.22: val gets replaced with final var.
> 새로운 Lombok 1.18.22 에서 `val`은 final var로 대체 되었다.

## Overview
You can use val as the type of a local variable declaration instead of actually writing the type. When you do this, the type will be inferred from the initializer expression. The local variable will also be made final. This feature works on local variables and on foreach loops only, not on fields. The initializer expression is required.

> 당신은 실제로 타입을 쓰는 것 대신에 지역 변수 타입의 선언으로 `val`을 사용할 수 있다. 이것을 사용하면, 타입이 초기화 표현식으로 추론될 수 있다. 지역 변수는 또한 final로 만들어진다.
이 기능은 지역 변수와 foreach 반복문에서만 사용할 수 있고, 필드로는 사용할 수 없다.
초기화하는 표현식이 필요하다.

val is actually a 'type' of sorts, and exists as a real class in the lombok package. You must import it for val to work (or use lombok.val as the type). The existence of this type on a local variable declaration triggers both the adding of the final keyword as well as copying the type of the initializing expression which overwrites the 'fake' val type.

> `val`은 실제로 `타입`의 한 종류이고, lombok 패키지 내에 실제로 존재하는 클래스이다.
`val`이 작동하기 위해서는 import를 해야한다(또는 `lombok.val`을 타입으로 사용하거나).
지역 변수 선언할 때 이 타입의 존재는 두 가지를 일으킨다. final 키워드를 붙여주는 것 뿐 아니라 fake `val`타입으로 초기화하는 표현식의 타입을 복사하여 덮어씌워준다.

WARNING: This feature does not currently work in NetBeans.

> 주의: 이 기능은 NetBeans에서는 작동하지 않는다.

### With Lombok
```
import java.util.ArrayList;
import java.util.HashMap;
import lombok.val;

public class ValExample {
  public String example() {
    val example = new ArrayList<String>();
    example.add("Hello, World!");
    val foo = example.get(0);
    return foo.toLowerCase();
  }
  
  public void example2() {
    val map = new HashMap<Integer, String>();
    map.put(0, "zero");
    map.put(5, "five");
    for (val entry : map.entrySet()) {
      System.out.printf("%d: %s\n", entry.getKey(), entry.getValue());
    }
  }
}
```

### Vanilla Java
```
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class ValExample {
  public String example() {
    final ArrayList<String> example = new ArrayList<String>();
    example.add("Hello, World!");
    final String foo = example.get(0);
    return foo.toLowerCase();
  }
  
  public void example2() {
    final HashMap<Integer, String> map = new HashMap<Integer, String>();
    map.put(0, "zero");
    map.put(5, "five");
    for (final Map.Entry<Integer, String> entry : map.entrySet()) {
      System.out.printf("%d: %s\n", entry.getKey(), entry.getValue());
    }
  }
}
```

### Supported configuration keys:
> 지원되는 설정 키
```
lombok.val.flagUsage = [warning|error](default: not set)
```
Lombok will flag any usage of val as a warning or error if configured.
> Lombok은 설정하면 `val`의 사용을 warning 또는 error로 표시할 수 있다.

### Small Print

For compound types, the most common superclass is inferred, not any shared interfaces. For example, `bool ? new HashSet() : new ArrayList()` is an expression with a compound type: The result is both AbstractCollection as well as Serializable. The type inferred will be AbstractCollection, as that is a class, whereas Serializable is an interface.

> 혼합된 타입을 위해서, 공유된 인터페이스는 추론되지 않고 가장 일반적인 슈퍼클래스가 추론된다.
예를 들면, `bool ? new HashSet() : new ArrayList()` 같은 혼합된 타입: 결과는 `AbstractCollection`과 `Serializable` 둘 다 추론된다. 타입은 `AbstractCollection`은 클래스로, `Serializable`은 인터페이스로 추론된다.

In ambiguous cases, such as when the initializer expression is null, java.lang.Object is inferred.

> null로 초기화 되는 모호한 경우, `java.lang.Object`가 추론된다.

<hr>

## var

### Mutably! Hassle-free local variables.
> 가변적으로! 혼란이 없는 지역 변수.

var was promoted to the main package in lombok 1.16.20; given that JEP 286 establishes expectations, and lombok's take on var follows these, we've decided to promote var eventhough the feature remains controversial.

> `var`는 lombok 1.16.20 메인 패키지에서 홍보되었다; `JEP 286`이 기대를 설정하고, lombok이 이를 따라서 `var`를 가져간 것을 고려하여, 우리는 `var`가 논란이 많은 기능으로 남아 있을지라도 홍보하기로 결정했다.

var was introduced in lombok 1.16.12 as experimental feature.

> `var`는 lombok 1.16.12에서 실험적인 기능으로 소개되었다.

### Overview

`var` works exactly like `val`, except the local variable is not marked as `final`.

> `var`는 `val`과 지역 변수에는 `final`이 붙지 않는 다는 것을 제외하고 정확히 똑같이 동작한다.

The type is still entirely derived from the mandatory initializer expression, and any further assignments, while now legal (because the variable is no longer final), aren't looked at to determine the appropriate type.
For example, `var x = "Hello"; x = Color.RED;` does not work; the type of x will be inferred to be `java.lang.String` and thus, the `x = Color.RED` assignment will fail. If the type of x was inferred to be `java.lang.Object` this code would have compiled, but that's not how `var` works.

> 타입은 여전히 필수적인 초기화 식에서 전적으로 추정되고, 추가적인 할당은 이제 합법적이지만(변수가 더이상 `final`이 아니기 때문에) 적절한 타입으로 결정되지 않는다.
예를 들면, `var x = "Hello"; x = Color.RED;`는 동작하지 않는다; x의 타입은 `java.lang.String`으로 추정되기 때문에, `x = Color.RED`는 실패할 것이다.
만약 x의 타입이 `java.lang.Object`로 추정되면 이 코드는 컴파일될 것이지만 그것은 `var`의 작동 방식이 아니다.

### Supported configuration keys:
> 지원되는 설정 키
```
lombok.var.flagUsage = [warning|error](default: not set)
```
Lombok will flag any usage of `var` as a warning or error if configured.

> Lombok은 설정하면 `var`의 사용을 warning 또는 error로 표시할 수 있다.

## @NonNull

### or: How I learned to stop worrying and love the NullPointerException.

> or: 내가 걱정하는 것을 멈추고 NullPointerException을 사랑하는 법을 배운 방법

@NonNull was introduced in lombok v0.11.10.

> @NonNull은 lombok v0.11.10.에서 소개되었다.

### Overview

You can use `@NonNull` on a record component, or a parameter of a method or constructor. This will cause to lombok generate a null-check statement for you.

> 당신은 record 요소 또는 메서드의 파라미터 또는 생성자에 `@NonNull`을 사용할 수 있다.
이것은 lombok이 null-check 식을 생성하도록 야기시켜줄 수 있다.
