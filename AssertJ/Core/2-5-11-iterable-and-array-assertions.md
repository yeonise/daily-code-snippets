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
