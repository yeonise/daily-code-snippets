## 2.5.11. Iterable and array assertions

### Checking iterables/arrays content

**iterable 객체 또는 배열의 내용 확인하기**

There are different flavors of `contains` assertion, here’s a table to help choose the most relevant one:

> 다양한 형태의 `contains` assertion이 있습니다. 다음은 당신이 가장 관련성이 높은 것을 선택하는 데 도움이 되는 표입니다.

| Assertion                   | Description                                                                                                                                                                                |
|:----------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `contains`                  | Verifies that the actual iterable/array contains the given values in any order                                                                                                             |
| `containsOnly`              | Verifies that the actual group contains only the given values and nothing else in any order and ignoring duplicates (i.e. once a value is found, its duplicates are also considered found) |
| `containsExactly`           | Verifies that the actual iterable/array contains exactly the given values and nothing else in order                                                                                        |
| `containsExactlyInAnyOrder` | Verifies that the actual iterable/array contains exactly the given values and nothing else in any order                                                                                    |
| `containsSequence`          | Verifies that the actual group contains the given sequence in the correct order and without extra values between the sequence values                                                       |
| `containsSubsequence`       | Verifies that the actual group contains the given subsequence in the correct order possibly with other values between them                                                                 |
| `containsOnlyOnce`          | Verifies that the actual iterable/array contains the given values only once                                                                                                                |
| `containsAnyOf`             | Verifies that the actual iterable/array contains at least one of the given values (like an or operator on the given values)                                                                |

<br/>

| Assertion                   | Description                                                        |
|:----------------------------|:-------------------------------------------------------------------|
| `contains`                  | 순서에 상관없이 값을 포함하고 있는지 확인합니다.                                        |
| `containsOnly`              | 중복은 무시하고 주어진 값만 포함하고 있는지 확인합니다. (즉, 값이 발견되면 중복 된 값도 찾은 것으로 여겨집니다.) |
| `containsExactly`           | 순서에 맞게 주어진 값을 정확히 포함하고 있는지 확인합니다.                                  |
| `containsExactlyInAnyOrder` | 순서와 상관없이 주어진 값을 정확히 포함하고 있는지 확인합니다.                                |
| `containsSequence`          | 주어진 그룹을 순서에 맞게 포함하고 있는지 확인합니다. 중간에 다른 객체가 들어가 순서가 달라지면 실패합니다.      |
| `containsSubsequence`       | 주어진 순서에 맞게 객체가 나열되어 있는지 확인합니다. 중간에 다른 객체가 들어가 있을 수 있습니다.           |
| `containsOnlyOnce`          | 해당 값을 단 하나만 갖고 있는지 확인합니다.                                          |
| `containsAnyOf`             | 주어진 값을 적어도 한 개 이상 포함하고 있는지 확인합니다.                                  |

<br/>

### Verify assertions on some elements

**특정 요소에 대한 assertion 확인하기**

**Satisfy**

**만족 여부**

You can assert that all or any elements verify the given assertions
with `allSatisfy` and `anySatisfy`, conversely `noneSatisfy` lets you assert
that no elements verify the given assertions.

> 당신은 모든 요소 또는 일부 요소에 대해 `모두 만족`, `하나라도 만족`, `하나도 만족하지 않음`을
> `allSatisfy`, `anySatisfy`, `noneSatisfy`를 통해 확인할 수 있습니다.

The given assertions are expressed with a `Consumer` (typically with a lambda).

> 주어진 assertion들은 일반적으로 람다를 사용한 `Consumer`를 통해 표현됩니다.

Examples:

``` java
List<TolkienCharacter> hobbits = list(frodo, sam, pippin);

// all elements must satisfy the given assertions
// 모든 요소들이 아래의 주장을 모두 만족해야 함
assertThat(hobbits).allSatisfy(character -> {
  assertThat(character.getRace()).isEqualTo(HOBBIT);
  assertThat(character.getName()).isNotEqualTo("Sauron");
});

// at least one element must satisfy the given assertions
// 하나라도 아래의 주장을 모두 만족해야 함
assertThat(hobbits).anySatisfy(character -> {
  assertThat(character.getRace()).isEqualTo(HOBBIT);
  assertThat(character.getName()).isEqualTo("Sam");
});

// no element must satisfy the given assertions
// 어떤 요소도 아래의 주장을 만족하지 않음
assertThat(hobbits).noneSatisfy(character -> assertThat(character.getRace()).isEqualTo(ELF));
```

**Match**

**일치 여부**

You can assert that all or any elements match the given `Predicate` with `allMatch` and `anyMatch`,
conversely `noneMatch` lets you assert that no elements verify the given predicate.

> 주어진 `Predicate`에 대해 모든 요소 또는 일부 요소가 `모두 일치`, `하나라도 일치`, `하나도 일치하지 않음`을
> `allMatch`, `anyMatch`, `noneMatch`를 통해 확인할 수 있습니다.

Examples:

``` java
List<TolkienCharacter> hobbits = list(frodo, sam, pippin);

assertThat(hobbits).allMatch(character -> character.getRace() == HOBBIT, "hobbits") // 모든 race가 hobbits인지 확인
                   .anyMatch(character -> character.getName().contains("pp")) // pp라는 이름을 갖는 요소가 1개라도 존재하는지 확인
                   .noneMatch(character -> character.getRace() == ORC); // race가 ORC인 요소가 하나도 없는지 확인
```

You can pass a predicate description to make the error message more explicit if the assertion fails.

> assertion이 실패할 경우 설명을 전달하여 오류 메시지를 더 명확하게 만들 수 있습니다.
