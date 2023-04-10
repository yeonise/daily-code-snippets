## 2.5.1. A Simple Example

Let’s start with a simple example showing a few important things.

> 몇 가지 중요한 것들을 보여주는 간단한 예부터 시작하겠습니다.

<br/>

``` java
import static org.assertj.core.api.Assertions.assertThat; // 1. import

import org.junit.jupiter.api.Test;

public class SimpleAssertionsExample {

  @Test
  void a_few_simple_assertions() {
    assertThat("The Lord of the Rings")  // 2. 테스트 대상이 되는 하나의 객체를 파라미터로 전달  
                   .isNotNull() // 3. 코드 자동 완성 기능을 사용하여 탐색한 뒤 원하는 assertion을 호출
                   .startsWith("The") // 4. 계속해서 필요한 assertions 체이닝 가능
                   .contains("Lord") 
                   .endsWith("Rings"); 
  }
  
}
```

Except for `isNotNull` which is a base assertion,
the other assertions are `String` specific as our object under test is a `String`.

> 기본 assertion인 `isNotNull`을 제외한 나머지 assertions은 `String`에 특정된 assertion입니다.
