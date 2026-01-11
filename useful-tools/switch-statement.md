---
description: "복잡한 `if ... else if` 구문을 효율적으로 관리할 수 있게 해주는 도구입니다."
---

# 🔄 Switch 문

`switch` 및 `case` 구문은 대부분의 경우 `Norm` 규칙에 어긋나므로 일반적인 과제에서는 사용할 수 없습니다.

하지만 일부 시험(Exam)에서는 매우 유용하며, 시험 중에는 `Norm`이 적용되지 않으므로 이때는 자유롭게 사용하실 수 있습니다.

### Switch 문 개요

`switch` 및 `case` 구문은 여러 개의 조건부 분기 작업을 제어할 때 코드의 가독성을 높여줍니다.

### 예시

`exam rank 02 - level 2`의 연습 문제 중 하나인 [`do_op`](../exams/exam-rank-02/level-2/do_op.md) 문제를 예로 들어보겠습니다. 해당 문제를 먼저 읽어보신 후 아래 코드를 비교해 보세요.

먼저 `if` 및 `else if` 구문을 사용하여 작성한 코드와, 동일한 로직을 `switch` 구문으로 작성한 코드를 비교해 보겠습니다.

#### 1. If ... else if 방식

<pre class="language-c" data-overflow="wrap" data-line-numbers><code class="lang-c">if (av[2][0] == '+')
    printf("%d", atoi(av[1]) + atoi(av[3]));
else if (av[2][0] == '-')
    printf("%d", atoi(av[1]) - atoi(av[3]));
else if (av[2][0] == '*')
    printf("%d", atoi(av[1]) * atoi(av[3]));
else if (av[2][0] == '/')
    printf("%d", atoi(av[1]) / atoi(av[3]));
else if (av[2][0] == '%')
    printf("%d", atoi(av[1]) % atoi(av[3]));
</code></pre>

#### 2. Switch 방식 (권장)

{% code overflow="wrap" lineNumbers="true" %}
```c
switch (av[2][0])
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
