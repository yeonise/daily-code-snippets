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

NEW in Lombok 1.14.0: To put annotations on the `other` parameter of the `equals` (and, if relevant, `canEqual`) method, you can use `onParam=@__({@AnnotationsHere})`. Be careful though! This is an experimental feature. For more details see the documentation on the `onX` feature.

> NEW in Lombok 1.14.0: `equals`(만약 관련 있다면,`canEqual`)메서드의 다른 `other` 파라미터에 애노테이션을 붙이면, `onParam=@__({@AnnotationsHere})`를 사용할 수 있다. 그렇지만 주의해라! 이것은 실험적 기능이다. 더 자세한 내용은 `onX` 문서에서 볼 수 있다.

NEW in Lombok 1.18.16: The result of the generated `hashCode()` can be cached by setting `cacheStrategy` to a value other than `CacheStrategy.NEVER`. Do not use this if objects of the annotated class can be modified in any way that would lead to the result of `hashCode()` changing.

> NEW in Lombok 1.18.16: 생성된 `hashCode()`의 결과는 `CacheStrategy.NEVER` 이외의 값에 `cacheStrategy` 설정에 의해서 캐시될 수 있다. 애노테이션이 달린 클래스가 `hashCode()`의 변경 결과로 이어지는 수정이 생길 수 있는 경우 사용하지 마라.

### With Lombok
```
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  @EqualsAndHashCode.Exclude private Shape shape = new Square(5, 10);
  private String[] tags;
  @EqualsAndHashCode.Exclude private int id;
  
  public String getName() {
    return this.name;
  }
  
  @EqualsAndHashCode(callSuper=true)
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

public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  @Override public boolean equals(Object o) {
    if (o == this) return true;
    if (!(o instanceof EqualsAndHashCodeExample)) return false;
    EqualsAndHashCodeExample other = (EqualsAndHashCodeExample) o;
    if (!other.canEqual((Object)this)) return false;
    if (this.getName() == null ? other.getName() != null : !this.getName().equals(other.getName())) return false;
    if (Double.compare(this.score, other.score) != 0) return false;
    if (!Arrays.deepEquals(this.tags, other.tags)) return false;
    return true;
  }
  
  @Override public int hashCode() {
    final int PRIME = 59;
    int result = 1;
    final long temp1 = Double.doubleToLongBits(this.score);
    result = (result*PRIME) + (this.name == null ? 43 : this.name.hashCode());
    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
    result = (result*PRIME) + Arrays.deepHashCode(this.tags);
    return result;
  }
  
  protected boolean canEqual(Object other) {
    return other instanceof EqualsAndHashCodeExample;
  }
  
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
    
    @Override public boolean equals(Object o) {
      if (o == this) return true;
      if (!(o instanceof Square)) return false;
      Square other = (Square) o;
      if (!other.canEqual((Object)this)) return false;
      if (!super.equals(o)) return false;
      if (this.width != other.width) return false;
      if (this.height != other.height) return false;
      return true;
    }
    
    @Override public int hashCode() {
      final int PRIME = 59;
      int result = 1;
      result = (result*PRIME) + super.hashCode();
      result = (result*PRIME) + this.width;
      result = (result*PRIME) + this.height;
      return result;
    }
    
    protected boolean canEqual(Object other) {
      return other instanceof Square;
    }
  }
}
```

## @Data

### All together now: A shortcut for `@ToString`, `@EqualsAndHashCode`, `@Getter` on all fields, `@Setter` on all non-final fields, and `@RequiredArgsConstructor`!
> 모든 것을 다같이 사용: 짧은 `@ToString, `@EqualsAndHashCode`, 모든 필드에 `@Getter`, non-final 필드에 `@Setter`, 그리고 `@RequiredArgsConstructor`

### OverView

`@Data` is a convenient shortcut annotation that bundles the features of `@ToString`, `@EqualsAndHashCode`, `@Getter` / `@Setter` and `@RequiredArgsConstructor` together: In other words, `@Data` generates all the boilerplate that is normally associated with simple POJOs (Plain Old Java Objects) and beans: getters for all fields, setters for all non-final fields, and appropriate `toString`, `equals` and `hashCode` implementations that involve the fields of the class, and a constructor that initializes all final fields, as well as all non-final fields with no initializer that have been marked with `@NonNull`, in order to ensure the field is never null.

> `@Data`는 편리한 `@ToString`, `@EqualsAndHashCode`, `@Getter` / `@Setter` 그리고 `@RequiredArgsConstructor` 기능들의 묶음 애노테이션이다: 다시 말해서, `@Data`는 보통 POJOs와 beans와 관련있는 모든 상용구들을 생성해준다: 모든 필드에 getter, 모든 non-final 필드에 setter, 클래스의 필드들을 포함하는 적절한 `toString`, `equals`, `hashCode`의 구현, 그리고 필드가 절대 null이 아님을 보장하기 위해서 `@NonNull`이 붙어진 생성자에 없는 non-final 필드 뿐만 아니라 모든 final 필드들을 초기화하는 생성자.

`@Data` is like having implicit `@Getter`, `@Setter`, `@ToString`,` @EqualsAndHashCode` and `@RequiredArgsConstructor` annotations on the class (except that no constructor will be generated if any explicitly written constructors already exist). However, the parameters of these annotations (such as `callSuper`, `includeFieldNames` and `exclude`) cannot be set with `@Data`. If you need to set non-default values for any of these parameters, just add those annotations explicitly; `@Data` is smart enough to defer to those annotations.

> `@Data`는 클래스에 암시적으로 `@Getter`, `@Setter`, `@ToString`,` @EqualsAndHashCode` 그리고 `@RequiredArgsConstructor`가 붙어있는 것으로 볼 수 있다(이미 명시적으로 작성된 생성자가 존재하는 경우 생성자가 생성되지 않는다는 것을 제외하고). 그러나, 이 애노테이션들(`callSuper`, `includeFieldNames`, `exclude`와 같은)의 파라미터들은 `@Data`로 설정되지 않는다. 만약 어떤 파라미터에 non-default 값을 넣고 싶다면, 이 애노태이션을 명시적으로 붙이면 된다; `@Data`는 이 애노테이션을 따를 만큼 똑똑하다.

All generated getters and setters will be `public`. To override the access level, annotate the field or class with an explicit `@Setter` and/or `@Getter` annotation. You can also use this annotation (by combining it with `AccessLevel.NONE`) to suppress generating a getter and/or setter altogether.

> 모든 생성된 getter와 setter들은 `public`이다. 접근 레벨에서 override 하면, 필드 또는 클래스에 명시적으로 `@Setter` 또는 `@Getter` 애노테이션을 붙인다. 또한 이 애노테이션을 (`AccessLevel.NONE`와 결합해서) 사용해서 getter와 setter가 전부 생성되는 것을 막을 수 있다.

All fields marked as `transient` will not be considered for `hashCode` and `equals`. All static fields will be skipped entirely (not considered for any of the generated methods, and no setter/getter will be made for them).

> `transient`로 마크된 모든 필드는 `hashCode`와 `equals`로 고려되지 않는다. 오든 static 필드는 전적으로 스킵된다(모든 생성된 메서드가 고려되지 않고 setter/getter는 만들어지지 않는다.)

If the class already contains a method with the same name and parameter count as any method that would normally be generated, that method is not generated, and no warning or error is emitted. For example, if you already have a method with signature `equals(AnyType param)`, no `equals` method will be generated, even though technically it might be an entirely different method due to having different parameter types. The same rule applies to the constructor (any explicit constructor will prevent `@Data` from generating one), as well as `toString`, `equals`, and all getters and setters. You can mark any constructor or method with `@lombok.experimental.Tolerate` to hide them from lombok.

> 만약 클래스에 이미 같은 이름과 파라미터 개수를 가지는 일반적으로 생성된 메서드가 존재한다면, 그 메서드는 생성되지 않는다, 그리고 warning이나 error가 발생하지 않는다. 예를 들면 만약 `equals(AnyType param)`이 이미 있다면 `equals`메서드는 생성되지 않는다, 비록 기술적으로 이것이 다른 파라미터 타입을 가지고 있어서 전적으로 다른 메서드 일지라도 생성되지 않는다. `toString`, `equals` 그리고 getter, setter 뿐만 아니라 생성자에도 같은 규칙이 적용된다(명시적인 생성자는 `@Data`가 하나를 생성하는 것을 막는다). 생성자나 메서드에 `@lombok.experimental.Tolerate`를 붙이면 lombok으로 부터 숨긴다.

`@Data` can handle generics parameters for fields just fine. In order to reduce the boilerplate when constructing objects for classes with generics, you can use the `staticConstructor` parameter to generate a private constructor, as well as a static method that returns a new instance. This way, javac will infer the variable name. Thus, by declaring like so: `@Data(staticConstructor="of") class Foo<T> { private T x;}` you can create new instances of `Foo` by writing: `Foo.of(5);` instead of having to write: `new Foo<Integer>(5);`.

> `@Data`는 필드에 대한 generics 파라미터를 잘 다룬다. generics를 가지는 클래스의 객체를 생성할 때 상용구를 줄이기 위해서, private 생성자를 생성할 때 `staticConstructor` 파라미터를 사용할 수 있다, 새로운 인스턴스를 반환하는 static 메서드 뿐 아니라. 이 경우에, javac는 변하기 쉬운 이름을 추론한다. 따라서, 다음과 같이 정의한다: `@Data(staticConstructor="of") class Foo<T> { private T x;}`  
다음처럼 작성하면 `new Foo<Integer>(5);`와 같이 작성하는 대신에 `Foo`의 인스턴스를 생성할 수 있다: `Foo.of(5)`

### With Lombok
```

import lombok.AccessLevel;
import lombok.Setter;
import lombok.Data;
import lombok.ToString;

@Data public class DataExample {
  private final String name;
  @Setter(AccessLevel.PACKAGE) private int age;
  private double score;
  private String[] tags;
  
  @ToString(includeFieldNames=true)
  @Data(staticConstructor="of")
  public static class Exercise<T> {
    private final String name;
    private final T value;
  }
}
```

### Vanilla Java
```
import java.util.Arrays;

public class DataExample {
  private final String name;
  private int age;
  private double score;
  private String[] tags;
  
  public DataExample(String name) {
    this.name = name;
  }
  
  public String getName() {
    return this.name;
  }
  
  void setAge(int age) {
    this.age = age;
  }
  
  public int getAge() {
    return this.age;
  }
  
  public void setScore(double score) {
    this.score = score;
  }
  
  public double getScore() {
    return this.score;
  }
  
  public String[] getTags() {
    return this.tags;
  }
  
  public void setTags(String[] tags) {
    this.tags = tags;
  }
  
  @Override public String toString() {
    return "DataExample(" + this.getName() + ", " + this.getAge() + ", " + this.getScore() + ", " + Arrays.deepToString(this.getTags()) + ")";
  }
  
  protected boolean canEqual(Object other) {
    return other instanceof DataExample;
  }
  
  @Override public boolean equals(Object o) {
    if (o == this) return true;
    if (!(o instanceof DataExample)) return false;
    DataExample other = (DataExample) o;
    if (!other.canEqual((Object)this)) return false;
    if (this.getName() == null ? other.getName() != null : !this.getName().equals(other.getName())) return false;
    if (this.getAge() != other.getAge()) return false;
    if (Double.compare(this.getScore(), other.getScore()) != 0) return false;
    if (!Arrays.deepEquals(this.getTags(), other.getTags())) return false;
    return true;
  }
  
  @Override public int hashCode() {
    final int PRIME = 59;
    int result = 1;
    final long temp1 = Double.doubleToLongBits(this.getScore());
    result = (result*PRIME) + (this.getName() == null ? 43 : this.getName().hashCode());
    result = (result*PRIME) + this.getAge();
    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
    result = (result*PRIME) + Arrays.deepHashCode(this.getTags());
    return result;
  }
  
  public static class Exercise<T> {
    private final String name;
    private final T value;
    
    private Exercise(String name, T value) {
      this.name = name;
      this.value = value;
    }
    
    public static <T> Exercise<T> of(String name, T value) {
      return new Exercise<T>(name, value);
    }
    
    public String getName() {
      return this.name;
    }
    
    public T getValue() {
      return this.value;
    }
    
    @Override public String toString() {
      return "Exercise(name=" + this.getName() + ", value=" + this.getValue() + ")";
    }
    
    protected boolean canEqual(Object other) {
      return other instanceof Exercise;
    }
    
    @Override public boolean equals(Object o) {
      if (o == this) return true;
      if (!(o instanceof Exercise)) return false;
      Exercise<?> other = (Exercise<?>) o;
      if (!other.canEqual((Object)this)) return false;
      if (this.getName() == null ? other.getValue() != null : !this.getName().equals(other.getName())) return false;
      if (this.getValue() == null ? other.getValue() != null : !this.getValue().equals(other.getValue())) return false;
      return true;
    }
    
    @Override public int hashCode() {
      final int PRIME = 59;
      int result = 1;
      result = (result*PRIME) + (this.getName() == null ? 43 : this.getName().hashCode());
      result = (result*PRIME) + (this.getValue() == null ? 43 : this.getValue().hashCode());
      return result;
    }
  }
}
```
