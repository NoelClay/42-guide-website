# ft\_isalpha

### 주제 (Subject)

{% code overflow="wrap" %}
```
ISALPHA(3) (simplified)

NAME
    isalpha -- alphabetic character test
SYNOPSIS
    int isalpha(int c)
DECRIPTION
    The isalpha() function tests for any character for which isupper(3) or islower(3) is true. The value of the argument must be resprensentable as an unsigned char or the value of EOF.

RETURN VALUES
    The isalpha() function return zero if the character tests false and returns non-zero if the character tests true.
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

이 함수에 대해 man 페이지는 그 자체로 설명이 충분하지만, 다른 말로 다시 설명해 드리겠습니다.

`isalpha()` 함수는 `int` 매개변수로 전달된 문자가 알파벳 문자(소문자 또는 대문자)인 경우 0이 아닌 값을 반환합니다.

만약 문자가 알파벳이 아닌 경우, `isalpha()` 함수는 `0`을 반환합니다.

### 힌트 (Hints)

{% code title="ft_isalpha.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_isalpha(int c)
{
    if (/* c 값이 ASCII 테이블의 소문자 중 하나이거나 c 값이 ASCII 테이블의 대문자 중 하나인 경우 */)
        return (/* 여러분이 선택하는 0이 아닌 값 */);
    return (0);
}
```
{% endcode %}

### 주석이 달린 해결책 (Commented solution)

{% hint style="danger" %}
에이, 정말 이 함수의 코드가 필요하신가요?
{% endhint %}

<details>

<summary>ft_isalpha</summary>

{% code title="ft_isalpha.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_isalpha(int c)
{
    /* 조건의 첫 번째 부분은 c가 대문자인지 확인합니다. */
    /* 조건의 두 번째 부분은 c가 소문자인지 확인합니다. */
    if ((c >= 65 && c <= 90) || (c >= 97 && c <= 122))
        return (c); // 여기서는 c가 알파벳인 경우 0이 아닌 값이 되므로 c를 반환하고 있습니다.
    return (0); // 이 지점에 도달하면 c는 알파벳이 아닙니다.
}
```
{% endcode %}

</details>
