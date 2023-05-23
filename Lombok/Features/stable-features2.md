## @Getter and @Setter
Never write `public int getFoo() {return foo;}` again.
> `public int getFoo() {return foo;}`를 절대 다시 쓰지 마라.

### Overview

You can annotate any field with `@Getter` and/or `@Setter`, to let lombok generate the default getter/setter automatically.
A default getter simply returns the field, and is named `getFoo` if the field is called `foo` (or `isFoo` if the field's type is `boolean`). A default setter is named `setFoo` if the field is called `foo`, returns `void`, and takes 1 parameter of the same type as the field. It simply sets the field to this value.

> 당신은 어느 필드에나 `@Getter` 그리고 `@Setter`를 붙일 수 있다, 이것은 lombok에게 자동적으로 default getter와 setter를 만들라고 시킨다.
defult getter는 단순히 필드를 반환한다, 그리고 필드명이 `foo` 이면 `getFoo`라고 이름 붙는다(또한 필드 타입이 boolean이면 `isFoo`). default setter는 필드명이 `foo`이면 `setFoo`라고 이름이 붙고, 반환 타입은 `void` 이며, 하나의 필드와 타입이 같은 파라미터를 가진다. 이것은 단순히 필드에 이 값을 넣어준다.

The generated getter/setter method will be `public` unless you explicitly specify an `AccessLevel`, as shown in the example below. Legal access levels are `PUBLIC`, `PROTECTED`, `PACKAGE`, and `PRIVATE`.

> 생성된 getter/settet 메서드는 아래 예시 처럼 `AccessLevel`을 명시적으로 특정하지 않는 한 `public`이다. 가능한 access level은 `PUBLIC`, `PROTECTED`, `PACKAGE`, `PRIVATE` 이다.

You can also put a `@Getter` and/or `@Setter` annotation on a class. In that case, it's as if you annotate all the non-static fields in that class with the annotation.

> 당신은 또한 클래스에 `@Getter`, `@Setter`를 붙일 수 있다. 그 경우, 클래스에 static이 아닌 모든 필드에 애노테이션을 붙이는 것과 같다.

You can always manually disable getter/setter generation for any field by using the special `AccessLevel.NONE` access level. This lets you override the behaviour of a `@Getter`, `@Setter` or `@Data` annotation on a class.

> 당신은 항상 모든 필드에 `AccessLevel.NONE` access level을 사용함으로써 getter/setter 생성을 수동으로 막을 수 있다. 이는 `@Getter`, `@Setter`, `@Data` 애노테이션의 행동을 override 하게 할 수 있다.

To put annotations on the generated method, you can use `onMethod=@__({@AnnotationsHere});` to put annotations on the only parameter of a generated setter method, you can use `onParam=@__({@AnnotationsHere})`. Be careful though! This is an experimental feature. For more details see the documentation on the `onX` feature.

> 생성된 메서드에 애노테이션을 붙이면 , `onMethod=@__({@AnnotationsHere});` 를 사용할 수 있고 setter 메서드의 파라미터에만 애노테이션을 붙이면, `onParam=@__({@AnnotationsHere})` 를 사용할 수 있다. 그렇지만 조심해야 한다! 이것은 실험적인 기능이다. 더 자세한 부분은 `onX` 기능의 문서를 보면 된다.

NEW in lombok v1.12.0: javadoc on the field will now be copied to generated getters and setters. Normally, all text is copied, and `@return` is moved to the getter, whilst `@param` lines are moved to the setter. Moved means: Deleted from the field's javadoc. It is also possible to define unique text for each getter/setter. To do that, you create a 'section' named `GETTER` and/or `SETTER`. A section is a line in your javadoc containing 2 or more dashes, then the text 'GETTER' or 'SETTER', followed by 2 or more dashes, and nothing else on the line. If you use sections, `@return` and `@param` stripping for that section is no longer done (move the `@return` or `@param` line into the section).

> lombok V1.12.0에서 새로운 것 : 이제 생성된 getter와 setter에 javadoc이 복사된다. 보통, 모든 텍스트는 복사되고, `@param` 라인이 setter로 이동하는 동안 `@return`은 getter로 이동한다. 이동한다: 필드의 javadoc에서 삭제된다. 이것은 또한 getter/setter 각각에 유일한 텍스트를 정의할 수 있다. 그렇게 하려면, `GETTER` and/or `SETTER`라는 'section'을 만든다. section은 javadoc에 있는 2개 이상의 대시 그리고 `GETTER` 또는 `SETTER` 텍스트 그 뒤에 2개 이상의 대시가 오고 그외에 아무것도 없는 라인이다. 만약 section을 사용한다면, 해당 섹션에 대한 `@return`과 `@param`은 더 이상 벗겨지지 않는다(`@return`또는 `@param` 라인이 section 안으로 이동하는 것).

### With Lombok
```
import lombok.AccessLevel;
import lombok.Getter;
import lombok.Setter;

public class GetterSetterExample {
  /**
   * Age of the person. Water is wet.
   * 
   * @param age New value for this person's age. Sky is blue.
   * @return The current value of this person's age. Circles are round.
   */
  @Getter @Setter private int age = 10;
  
  /**
   * Name of the person.
   * -- SETTER --
   * Changes the name of this person.
   * 
   * @param name The new value.
   */
  @Setter(AccessLevel.PROTECTED) private String name;
  
  @Override public String toString() {
    return String.format("%s (age: %d)", name, age);
  }
}
```

### Vanila Java
```

public class GetterSetterExample {
  /**
   * Age of the person. Water is wet.
   */
  private int age = 10;

  /**
   * Name of the person.
   */
  private String name;
  
  @Override public String toString() {
    return String.format("%s (age: %d)", name, age);
  }
  
  /**
   * Age of the person. Water is wet.
   *
   * @return The current value of this person's age. Circles are round.
   */
  public int getAge() {
    return age;
  }
  
  /**
   * Age of the person. Water is wet.
   *
   * @param age New value for this person's age. Sky is blue.
   */
  public void setAge(int age) {
    this.age = age;
  }
  
  /**
   * Changes the name of this person.
   *
   * @param name The new value.
   */
  protected void setName(String name) {
    this.name = name;
  }
}
```

<hr><br><br>

## @ToString
No need to start a debugger to see your fields: Just let lombok generate a toString for you!

> 필드를 보기 위해서 debugger를 시작할 필요가 없다: 그저 lombok에게 toString을 생성하도록 시켜라!

Annotating a class with `@ToString` will cause lombok to generate an implementation of the `toString()` method. You use configuration options to specify whether field names should be included but otherwise the format is fixed: the class name followed by parentheses containing fields separated by commas, e.g. `MyClass(foo=123, bar=234)`.

> 클래스에 `@ToString`을 붙이는 것은 lombok이 `toString()` 메서드를 구현하도록 시킨다. 당신은 설정 옵션을 통해서 필드의 이름들을 포함시킬 건지 아니면 포맷을 고칠 건지 특정할 수 있다: 클래스명 다음에 나오는 괄호는 콤마로 분리된 필드들을 포함한다. 예를 들면 `MyClass(foo=123, bar=234)`.

By setting the `includeFieldNames` parameter to true you can add some clarity (but also quite some length) to the output of the `toString()` method.

> `includeFieldNames` 파라미터를 true로 설정하면 `toString()` 메서드의 출력에 명확성(약간의 길이 뿐만 아니라)을 추가할 수 있다.
