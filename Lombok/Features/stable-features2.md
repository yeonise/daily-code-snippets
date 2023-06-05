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

By default, all non-static fields will be printed. If you want to skip some fields, you can annotate these fields with `@ToString.Exclude`. Alternatively, you can specify exactly which fields you wish to be used by using `@ToString(onlyExplicitlyIncluded = true)`, then marking each field you want to include with `@ToString.Include`.

> 기본적으로, 모든 non-static 필드는 출력된다. 몇몇 필드는 출력되지 않기를 원한다면, `@ToString.Exclude`를 필드에 붙일 수 있다. 또는 어떤 필드는 명확하게 나타나기를 원한다면 `@ToString(onlyExplicitlyIncluded = true)`를 사용할 수 있다, 그리고 원하는 필드에 `@ToString.Include`를 붙여서 사용한다.

By setting `callSuper` to true, you can include the output of the superclass implementation of `toString` to the output. Be aware that the default implementation of `toString()` in `java.lang.Object` is pretty much meaningless, so you probably don't want to do this unless you are extending another class.

> `callSuper`를 true로 설정하면, 출력에 슈퍼 클래스의 `toString`을 포함할 수 있다. `java.lang.Object`의 기본적인 `toString()`은 꽤 의미가 없다는 것을 알아둬라, 그래서 아마 다른 클래스를 확장하지 않는 한 이것을 원하지 않을 것이다.

You can also include the output of a method call in your `toString`. Only instance (non-static) methods that take no arguments can be included. To do so, mark the method with `@ToString.Include`.

> 또한 `toString` 메서드가 출력에 포함되기를 원할 것이다. 오직 인자를 가지지 않는 (non-static) 메서드만 포함될 수 있다. 그렇게 하기 위해서는 메서드에 `@ToString.Include`를 붙여라.

You can change the name used to identify the member with `@ToString.Include(name = "some other name")`, and you can change the order in which the members are printed via `@ToString.Include(rank = -1)`. Members without a rank are considered to have rank 0, members of a higher rank are printed first, and members of the same rank are printed in the same order they appear in the source file.

> 멤버변수를 식별하기 위해서 `@ToString.Include(name = "some other name")`로 이름을 변경할 수 있고, `@ToString.Include(rank = -1)`를 통해서 출력되는 멤버변수의 순서를 변경할 수 있다. rank가 없는 멤버변수는 rank0으로 고려된다, 높은 rank의 멤버변수가 먼저 출력되고, 같은 rank의 멤버변수는 소스 파일에 나타나는 순서대로 출력된다.

### With Lombok
```

import lombok.ToString;

@ToString
public class ToStringExample {
  private static final int STATIC_VAR = 10;
  private String name;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  @ToString.Exclude private int id;
  
  public String getName() {
    return this.name;
  }
  
  @ToString(callSuper=true, includeFieldNames=true)
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
  }
}
```

### Vanilla Java
```

import java.util.Arrays;

public class ToStringExample {
  private static final int STATIC_VAR = 10;
  private String name;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
    
    @Override public String toString() {
      return "Square(super=" + super.toString() + ", width=" + this.width + ", height=" + this.height + ")";
    }
  }
  
  @Override public String toString() {
    return "ToStringExample(" + this.getName() + ", " + this.shape + ", " + Arrays.deepToString(this.tags) + ")";
  }
}
```

## @EqualsAndHashCode

### Equality made easy: Generates `hashCode` and `equals` implementations from the fields of your object.

> 객체의 필드들의 `hashCode`와 `equals`의 구현을 만들어준다.

Any class definition may be annotated with `@EqualsAndHashCode` to let lombok generate implementations of the `equals(Object other)` and `hashCode()` methods. By default, it'll use all non-static, non-transient fields, but you can modify which fields are used (and even specify that the output of various methods is to be used) by marking type members with `@EqualsAndHashCode.Include` or `@EqualsAndHashCode.Exclude`. Alternatively, you can specify exactly which fields or methods you wish to be used by marking them with `@EqualsAndHashCode.Include` and using `@EqualsAndHashCode(onlyExplicitlyIncluded = true)`.

> `@EqualsAndHashCode`를 붙인 클래스를 정의하면 lombok이 `equals(Object other)`와 `hashCode` 메서드를 생성하도록 한다. 기본적으로 non-static, non-transient 필드에만 사용되지만 타입 멤버에 `@EqualsAndHashCode.Include` 또는 `@EqualsAndHashCode.Exclude`를 붙여줘서 필드를 사용되도록(그리고 다양한 메서드의 출력이 사용되도록 특정) 변경할 수 있다. 또는 필드나 메서드에 `@EqualsAndHashCode.Include` 붙이거나 `@EqualsAndHashCode(onlyExplicitlyIncluded = true)`를 사용해서 사용되도록 정확하게 지정할 수 있다.

If applying `@EqualsAndHashCode` to a class that extends another, this feature gets a bit trickier. Normally, auto-generating an `equals` and `hashCode` method for such classes is a bad idea, as the superclass also defines fields, which also need equals/hashCode code but this code will not be generated. By setting `callSuper` to true, you can include the `equals` and `hashCode` methods of your superclass in the generated methods. For `hashCode`, the result of `super.hashCode()` is included in the hash algorithm, and for `equals`, the generated method will return false if the super implementation thinks it is not equal to the passed in object. Be aware that not all `equals` implementations handle this situation properly. However, lombok-generated `equals` implementations do handle this situation properly, so you can safely call your superclass equals if it, too, has a lombok-generated `equals` method. If you have an explicit superclass you are forced to supply some value for `callSuper` to acknowledge that you've considered it; failure to do so results in a warning.

> 만약 다른 클래스를 확장하는 클래스에 `@EqualsAndHashCode`를 적용하면, 이 기능은 조금 까다로워진다. 보통 그런 클래스들에 `equals` 와 `hashCode`메서드를 자동 생성 하는 것은 좋지 않다, 슈퍼클래스 또한 필드를 정의하고 있고 그 필드들도 equals/hashCode 메서드를 필요로 하지만 이 코드는 생성되지 않기 때문이다. `callSuper`를 true로 설정해주면, 슈퍼클래스에도 `equals`와 `hashCode`메서드를 생성시키도록 할 수 있다. `hashCode`의 경우, `super.hashCode()`의 결과가 hash 알고리즘을 포함하게 되고, `equals`의 경우, 생성된 메서드는 통과된 객체가 슈퍼 클래스의 구현된 메서드에서 같지 않다고 생각되면 false를 리턴하게 된다. 그러나 모든 구현된 `equals` 메서드가 이 상황 처럼 적절하게 다뤄지지 않는 것을 인지해야 한다. 그러나, lombok이 생성한 `equals` 메서드는 이 상황을 적절하게 다룬다, 그래서 만약 슈퍼클래스도 lombok이 생성한 `equals` 메서드를 가지고 있다면 안전하게 슈퍼클래스의 equals를 호출할 수 있다. 만약 명시적인 슈퍼클래스를 가지고 있다면 이것을 고려했다는 것을 인지하기 위해서 `callSuper`를 제공해야 한다; 그렇게 하지 못했다면 경고가 표시된다.

Setting `callSuper` to true when you don't extend anything (you extend `java.lang.Object`) is a compile-time error, because it would turn the generated `equals()` and `hashCode()` implementations into having the same behaviour as simply inheriting these methods from `java.lang.Object`: only the same object will be equal to each other and will have the same hashCode. Not setting `callSuper` to true when you extend another class generates a warning, because unless the superclass has no (equality-important) fields, lombok cannot generate an implementation for you that takes into account the fields declared by your superclasses. You'll need to write your own implementations, or rely on the `callSuper` chaining facility. You can also use the `lombok.equalsAndHashCode.callSuper` config key.

> 아무것도(`java.lang.Object`는 확장한다) 확장하지 않았을 때 `callSuper`를 true로 설정하면 컴파일 타임 에러가 발생한다, 왜냐하면 `java.lang.Object`에서 상속받은 메서드와 같은 행위를 하도록 `equals()`와 `hashCode()`의 구현을 바꾸기 때문이다 : 같은 객체는 서로 동일하고 같은 hashCode를 가진다. 다른 클래스를 확장할 때 `callSuper`를 true로 설정하지 않으면 경고를 발생시킨다, 왜냐하면 슈퍼 클래스가 (동등이 중요한)필드를 가지지 않은 한, lombok은 슈퍼 클래스에 정의된 필드 정보를 사용하는 구현을 생성하지 않기 때문이다. 당신은 자신만의 구현이 필요할 것이다, 또는 `callSuper`의 연결 기능에 의존할 것이다. 또한 `lombok.equalsAndHashCode.callSuper` 설정 키를 사용할 수 있다.

NEW in Lombok 0.10: Unless your class is `final` and extends `java.lang.Object`, lombok generates a `canEqual` method which means JPA proxies can still be equal to their base class, but subclasses that add new state don't break the equals contract. The complicated reasons for why such a method is necessary are explained in this paper: `How to Write an Equality Method in Java`. If all classes in a hierarchy are a mix of scala case classes and classes with lombok-generated equals methods, all equality will 'just work'. If you need to write your own equals methods, you should always override `canEqual` if you change `equals` and `hashCode`.

> NEW in Lombok 0.10 : 클래스가 `final`이고 `java.lang.Object`를 확장하지 않는 한, lombok은 JPA proxi가 그들의 기본 클래스와 같은지 확인할 수 있는 `canEqual` 메서드를 생성한다, 그러나 새로운 상태를 추가하는 서브 클래스는 동등 계약을 깨지 않는다. 이 메서드가 필요한지에 대한 복잡한 이유는 이 논문에 설명되어 있다: `How to Write an Equality Method in Java`. 만약 모든 클래스의 계층구조가 스칼라 케이스 클래스들과 lombok이 생성한 equals 메서드를 가지는 클래스들의 혼합이라면 모든 equality는 '그냥 작동한다'. 만약 당신이 자신만의 equals 메서드를 작성하고 싶은 경우, `equals`와 `hachCode`를 바꾼다면 `canEqual`을 항상 오버라이드 해야 한다.
