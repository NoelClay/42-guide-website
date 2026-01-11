---
description: 설명: 복잡한 `if ... else if` 구문을 용이하게 하는 도구입니다.
---

# 🔄 Switch 문

`switch` 및 `case` 구문은 대부분의 경우 `Norm`에 포함되어 있지 않으므로 사용할 수 없습니다.

하지만 일부 시험에서는 유용할 수 있으며, 시험 중에는 `Norm`이 적용되지 않으므로 이때는 사용하실 수 있습니다.

### Switch 문

`switch` 및 `case` 구문은 복잡한 조건부 및 분기 작업을 제어하는 데 도움을 줍니다.

### 예시

저는 `exam rank 02 - level 2`의 연습 문제 중 하나인 [`do_op`](../exams/exam-rank-02/level-2/do_op.md) 문제를 예시로 들겠습니다. 해당 주제를 먼저 읽어보신 후 이리로 돌아와 주십시오.

먼저 `if` 및 `else if` 구문을 사용하여 작성하고, 그다음에는 완전히 동일한 내용을 `switch` 구문을 사용하여 작성하겠습니다.

#### If ... else if

<pre class="language-c" data-overflow="wrap" data-line-numbers><code class="lang-c">if (av[2][0] == '+')
    printf("%d", atoi(av[1]) + atoi(av[3]));
else if (av[2][0] == '-')
    printf("%d", atoi(av[1]) - atoi(av[3]));
else if (av[2][0] == '*')
<strong>    printf("%d", atoi(av[1]) * atoi(av[3]));
</strong><strong>else if (av[2][0] == '/')
</strong><strong>    printf("%d", atoi(av[1]) / atoi(av[3]));
</strong><strong>else if (av[2][0] == '%')
</strong><strong>    printf("%d", atoi(av[1]) % atoi(av[3]));
</strong></code></pre>

#### Switch

{% code overflow="wrap" lineNumbers="true" %}
```c
swtich (av[2][0])
{
    case '+':
        printf("%d", atoi(av[1]) + atoi(av[3]));
        break;
    case '-':
        printf("%d", atoi(av[1]) - atoi(av[3]));
        break;
    case '*':
        printf("%d", atoi(av[1]) * atoi(av[3]));
        break;
    case '/':
        printf("%d", atoi(av[1]) / atoi(av[3]));
        break;
    case '%':
        printf("%d", atoi(av[1]) % atoi(av[3]));
        break;
}
```
{% endcode %}

보시다시피 두 코드는 매우 유사하지만, 저는 개인적으로 `switch` 구문이 더 명확하고 작성하기 쉽다고 생각합니다.

`switch` 구문은 공간을 약간 더 차지하지만, 특정 상황에서는 유용할 수 있습니다.

이에 대한 자세한 내용을 [여기](https://www.w3schools.com/c/c_switch.php)에서 확인하실 수 있습니다.