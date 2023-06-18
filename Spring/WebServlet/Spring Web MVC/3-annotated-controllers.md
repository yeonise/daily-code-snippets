## Annotated Controllers

Spring MVC provides an annotation-based programming model where `@Controller` and `@RestController` components use annotations to express request mappings, request input, exception handling, and more. Annotated controllers have flexible method signatures and do not have to extend base classes nor implement specific interfaces. The following example shows a controller defined by annotations:

> 스프링 MVC는 어노테이션을 기반으로 한 프로그래밍 모델을 제공합니다.
> `@Controller`와 `@RestController`의 구성 요소들은 어노테이션을 사용하여 요청 매핑, 요청 입력, 예외 처리 등을 표현합니다.
> 주석이 달린 컨트롤러는 유연한 메서드 시그니처를 가지며 베이스 클래스를 확장하거나 특정 인터페이스를 구현할 필요가 없습니다.
> 다음 예제는 어노테이션으로 정의된 컨트롤러를 보여줍니다:

``` java
@Controller
public class HelloController {  // java

	@GetMapping("/hello")
	public String handle(Model model) {
		model.addAttribute("message", "Hello World!");
		return "index";
	}
}
```

``` kotlin
import org.springframework.ui.set

@Controller
class HelloController {  // kotlin

	@GetMapping("/hello")
	fun handle(model: Model): String {
		model["message"] = "Hello World!"
		return "index"
	}
}
```

> 위 예제에서는 메서드가 모델을 받아들이고 뷰 이름을 문자열로 반환하지만 이외에도 다른 많은 옵션이 존재하며 이 장의 뒷부분에서 설명합니다.
