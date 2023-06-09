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

<hr><br><br>

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

<hr><br><br>

## @NonNull

### or: How I learned to stop worrying and love the NullPointerException.

> or: 내가 걱정하는 것을 멈추고 NullPointerException을 사랑하는 법을 배운 방법

@NonNull was introduced in lombok v0.11.10.

> @NonNull은 lombok v0.11.10.에서 소개되었다.

### Overview

You can use `@NonNull` on a record component, or a parameter of a method or constructor. This will cause to lombok generate a null-check statement for you.

> 당신은 record 요소 또는 메서드의 파라미터 또는 생성자에 `@NonNull`을 사용할 수 있다.
이것은 lombok이 null-check 식을 생성하도록 야기시켜줄 수 있다.

Lombok has always treated various annotations generally named `@NonNull` on a field as a signal to generate a null-check if lombok generates an entire method or constructor for you, via for example `@Data`. However, using lombok's own `@lombok.NonNull` on a parameter or record component results in the insertion of the null-check at the top of that method.

> lombok은 만약 `@Data`를 통해서 롬복이 전체 메서드 또는 생성자를 생성한다면 null-check를 생성하는 신호로 일반적으로 필드에서 `@NonNull` 이라고 불리는 다양한 어노테이션을 항상 다뤄왔다.
그러나, lombok의 `@lombok.NonNull`을 파라미터 또는 record 요소에 사용하는 것은 메서드의 맨 위에 null-check를 넣는 결과를 발생시킨다.

The null-check looks like `if (param == null) throw new NullPointerException("param is marked non-null but is null");` and will be inserted at the very top of your method. For constructors, the null-check will be inserted immediately following any explicit `this()` or `super()` calls. For record components, the null-check will be inserted in the 'compact constructor' (the one that has no argument list at all), which will be generated if you have no constructor. If you have written out the record constructor in long form (with parameters matching your components exactly), then nothing happens - you'd have to annotate the parameters of this long-form constructor instead.
If a null-check is already present at the top, no additional null-check will be generated.

> null-check는 다음과 같이 보인다. `if (param == null) throw new NullPointerException("param is marked non-null but is null");`
그리고 null-check는 메서드의 가장 위에 삽입된다.
생성자에서, null-check는 `this()` 또는 `super()`의 명백한 호출에 즉시 삽입된다.
record 요소에서 생성자가 없는 경우 null-check는 'compact 생성자'(인자가 없는 생성자)에 삽입된다.
만약 record 생성자를 긴 형식(파라미터들이 모두 매칭되는)으로 작성한다면 아무것도 일어나지 않는다. - 대신에 긴 형식 생성자의 파라미터에 어노테이션을 붙여야 할 것이다.
만약 null-check가 이미 위에 있다면, 추가적인 null-check는 발생하지 않는다.

### With Lombok
```
import lombok.NonNull;

public class NonNullExample extends Something {
  private String name;
  
  public NonNullExample(@NonNull Person person) {
    super("Hello");
    this.name = person.getName();
  }
}
```
### Vanilla Java
```
import lombok.NonNull;

public class NonNullExample extends Something {
  private String name;
  
  public NonNullExample(@NonNull Person person) {
    super("Hello");
    if (person == null) {
      throw new NullPointerException("person is marked non-null but is null");
    }
    this.name = person.getName();
  }
}
```
### Supported configuration keys:
```
lombok.nonNull.exceptionType = [NullPointerException | IllegalArgumentException | JDK | Guava | Assertion] (default: NullPointerException).
```
When lombok generates a null-check `if` statement, by default, a `java.lang.NullPointerException` will be thrown with 'field name is marked non-null but is null' as the exception message. However, you can use `IllegalArgumentException` in this configuration key to have lombok throw that exception with this message instead. By using `Assertion`, an `assert` statement with the same message will be generated. The keys `JDK` or `Guava` result in an invocation to the standard nullcheck method of these two frameworks: `java.util.Objects.requireNonNull([field name here], "[field name here] is marked non-null but is null");` or `com.google.common.base.Preconditions.checkNotNull([field name here], "[field name here] is marked non-null but is null");` respectively.

> lombok이 `if`문에서 null-check를 발생시킬 때 default는 `java.lang.NullPointerException`을 'field name is marked non-null but is null' 메시지와 함께 발생시킨다. 그러나 `IllegalArgumentException`로 설정하면 lombok은 `IllegalArgumentException`을 발생시킨다. `Assertion`을 사용하면 동일한 메시지를 가진 `assert`문이 발생된다.
`JDK`와 `Guava`는 이 두 가지 프레임워크의 기본 nullcheck 메서드를 호출한다 : `java.util.Objects.requireNonNull([field name here], "[field name here] is marked non-null but is null");` 또는 `com.google.common.base.Preconditions.checkNotNull([field name here], "[field name here] is marked non-null but is null");`
```
lombok.nonNull.flagUsage = [warning | error] (default: not set)
```
Lombok will flag any usage of @NonNull as a warning or error if configured.

> lombok은 `@NonNull`의 사용에 warning 또는 error를 표시하도록 설정할 수 있다.

### Small Print

Lombok's detection scheme for already existing null-checks consists of scanning for if statements or assert statements that look just like lombok's own. Any 'throws' statement as the 'then' part of the if statement, whether in braces or not, counts. Any invocation to any method named `requireNonNull` or `checkNotNull` counts. The conditional of the if statement must look exactly like `PARAMNAME == null`; the assert statement must look exactly like `PARAMNAME != null`. The invocation to a `requireNonNull`-style method must be on its own (a statement which just invokes that method), or must be the expression of an assignment or variable declaration statement. The first statement in your method that is not such a null-check stops the process of inspecting for null-checks.

> Lombok의 이미 존재하는 null-check에 대한 탐지 방식은 lombok의 기능으로 보이는 if 문 또는 assert 문을 찾는 방식으로 이루어져 있다. if 문의 then 부분인 throws는 괄호가 있던지 없던지 계산된다. `requireNonNull` 또는 `checkNotNull` 메서드의 호출은 계산된다. if 문의 조건은 반드시 `PARAMNAME == null`과 같아야 하고 assert 문은 반드시 `PARAMNAME != null` 이어야 한다. `requireNonNull`스타일 메서드 호출은 자체적이거나(해당 메서드를 호출하는 구문) 할당 또는 변수 선언문의 표현식이어야 한다. null-check 가 아닌 메서드의 첫번째 구문은 null-check 검사 프로세스를 중지시킨다.

While `@Data` and other method-generating lombok annotations will trigger on various well-known annotations that signify the field must never be `@NonNull`, this feature only triggers on lombok's own `@NonNull` annotation from the `lombok` package.

> `@Data` 와 다른 메서드 생성 애노테이션은 다양한 잘 알려진 필드가 절대 `@NonNull`이 되서는 안되는 애노테이션을 발생시킨다, 이 기능은 오직 lombok 패키지의 lombok 자체의 `@NonNull` 애노테이션을 발생시킨다.

A `@NonNull` on a primitive parameter results in a warning. No null-check will be generated.

> primitive 파라미터의 `@NonNull`은 위험한 결과를 발생시킨다. null-check가 생성되지 않게 한다.

A `@NonNull` on a parameter of an abstract method used to generate a warning; starting with version 1.16.8, this is no longer the case, to acknowledge the notion that `@NonNull` also has a documentary role. For the same reason, you can annotate a method as `@NonNull`; this is allowed, generates no warning, and does not generate any code.

> abstract 메서드 파라미터의 `@NonNull`은 위험을 발생시킨다; 그러나 1.16.8 버전에서 시작하면, `@NonNull`은 documentary의 역할도 한다는 것을 알려주기 위해서 더 이상 그렇지 않다. 같은 이유로, 메서드에 `@NonNull`을 붙일 수 있다; 이것은 위험을 발생시키지 않고, 어떤 코드도 만들어내지 않는다는 것을 허용한다.

<hr><br><br>

## @Cleanup

Automatic resource management: Call your close() methods safely with no hassle.

> 자동 자원 관리: close() 메서드를 혼란 없이 안전하게 호출해라.

### OverView

You can use `@Cleanup` to ensure a given resource is automatically cleaned up before the code execution path exits your current scope. You do this by annotating any local variable declaration with the `@Cleanup` annotation like so:
`@Cleanup InputStream in = new FileInputStream("some/file");`
As a result, at the end of the scope you're in, `in.close()` is called. This call is guaranteed to run by way of a try/finally construct. Look at the example below to see how this works.

> 주어진 자원이 현재 스코프에 존재하는 코드 실행 후에 자동적으로 청소되도록 하는 `@Cleanup`을 사용할 수 있다. 당신은 이것을 지역 변수 선언에 `@Cleanup`을 붙임으로서 사용할 수 있다: `@Cleanup InputStream in = new FileInputStream("some/file");`
결과적으로, 현재 스코프의 끝에서 `in.close()`가 호출된다. 이것은 try/finally 구조를 통해 실행되는 것을 보장한다. 다음의 실행되는 예제를 보아라.

If the type of object you'd like to cleanup does not have a `close()` method, but some other no-argument method, you can specify the name of this method like so:
`@Cleanup("dispose") org.eclipse.swt.widgets.CoolBar bar = new CoolBar(parent, 0);`
By default, the cleanup method is presumed to be `close()`. A cleanup method that takes 1 or more arguments cannot be called via `@Cleanup`.

> 만약 cleanup을 실행하려는 객체가 `close` 메서드를 가지지 않는 경우, 그러나 인자가 없는 메서드가 있는 경우, 다음처럼 메서드의 이름을 특정할 수 있다:  
```
@Cleanup("dispose") 
org.eclipse.swt.widgets.CoolBar bar = new CoolBar(parent, 0);
```
> 기본적으로, cleanup 메서드는 `close()`로 추정된다. cleanup 메서드가 한개 또는 여러개의 인자를 가지는 경우 `@Cleanup`로 호출될 수 없다.

### With Lombok
```
import lombok.Cleanup;
import java.io.*;

public class CleanupExample {
  public static void main(String[] args) throws IOException {
    @Cleanup InputStream in = new FileInputStream(args[0]);
    @Cleanup OutputStream out = new FileOutputStream(args[1]);
    byte[] b = new byte[10000];
    while (true) {
      int r = in.read(b);
      if (r == -1) break;
      out.write(b, 0, r);
    }
  }
}
```
### Vanilla Java
```
import java.io.*;

public class CleanupExample {
  public static void main(String[] args) throws IOException {
    InputStream in = new FileInputStream(args[0]);
    try {
      OutputStream out = new FileOutputStream(args[1]);
      try {
        byte[] b = new byte[10000];
        while (true) {
          int r = in.read(b);
          if (r == -1) break;
          out.write(b, 0, r);
        }
      } finally {
        if (out != null) {
          out.close();
        }
      }
    } finally {
      if (in != null) {
        in.close();
      }
    }
  }
}
```
### Small Print
In the finally block, the cleanup method is only called if the given resource is not `null`. However, if you use `delombok` on the code, a call to `lombok.Lombok.preventNullAnalysis(Object o)` is inserted to prevent warnings if static code analysis could determine that a null-check would not be needed. Compilation with `lombok.jar` on the classpath removes that method call, so there is no runtime dependency.

> finally 블록에서, cleanup 메서드는 주어진 자원이 `null`이 아닐 때만 호출된다. 그러나, `delombok`을 사용하면, `lombok.Lombok.preventNullAnalysis(Object o)`이 만약 static 코드 분석이 null-check가 필요없다고 결정했을 때의 위험을 예방하기 위해 삽입된다. `lombok.jar`에 의한 컴파일은 메서드 호출을 제거한다, 그러면 런타임 의존성이 사라진다.

If your code throws an exception, and the cleanup method call that is then triggered also throws an exception, then the original exception is hidden by the exception thrown by the cleanup call. You should not rely on this 'feature'. Preferably, lombok would like to generate code so that, if the main body has thrown an exception, any exception thrown by the close call is silently swallowed (but if the main body exited in any other way, exceptions by the close call will not be swallowed). The authors of lombok do not currently know of a feasible way to implement this scheme, but if java updates allow it, or we find a way, we'll fix it.

> 만약 코드가 예외를 발생시켰을 경우, 그리고 cleanup 메서드가 호출했을 경우 또한 예외를 발생시킨다, 그러면 원래의 예외는 cleanup 호출에 의한 예외에 숨겨진다. 당신은 이 기능에 의존하면 안된다.
가급적이면, lombok은 코드를 이렇게 생성한다, main body가 예외를 발생시켰을 경우, 모든 예외는 close 호출에 의해 조용히 삼켜진다.(그러나 main body가 다른 방식에 의해 종료된 경우, 예외는 close 호출에 의해 삼겨지지 않는다.)
lombok의 저자는 이 스킴을 구현하는 실현 가능한 방법을 정확히 알지 못한다, 그러나 java 업데이트가 이것을 허용해줬고, 우리는 방법을 찾아서, 그것을 고쳤다.

You do still need to handle any exception that the cleanup method can generate!

> 당신은 여전히 cleanup 메서드가 발생시킬 수 있는 예외를 처리해야 한다.
