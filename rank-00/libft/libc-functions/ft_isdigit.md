# ft\_isdigit

### 주제

{% code overflow="wrap" %}
```
ISDIGIT(3) (simplified)

NAME
    isdigit -- decimal-digit character test
SYNOPSIS
    int isdigit(int c)
DESCRIPTION
    The isdigit() function tests for a decimal digit character.
    The value of the argument must be representable as an unsigned char or the value of EOF.
RETURN VALUES
    The isdigit() function return zero if the character tests false and return non-zero if the character tests true.
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수에 대한 man 페이지는 스스로 충분히 설명하고 있지만, 저는 여전히 다른 말로 설명하겠습니다.

`isdigit()` 함수는 `int` 매개변수로 전달된 문자가 10진수 숫자 문자(0 - 9)인 경우 0이 아닌 값을 반환합니다.

문자가 10진수 숫자 문자가 아닌 경우, `isdigit()` 함수는 `0`을 반환합니다.

### 힌트

{% code title="ft_isdigit.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_isdigit(int c)
{
    if (/* c 값이 ASCII 테이블의 10진수 숫자 문자 중 하나인 경우 */)
        return (/* 원하는 0이 아닌 값 */);
    return (0);
}
```
{% endcode %}

### 주석 처리된 해답

{% hint style="danger" %}
정말로 이 함수에 대한 코드가 필요하신가요?
{% endhint %}

<details>

<summary>ft_isdigit</summary>

{% code title="ft_isdigit.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_isdigit(int c)
{
    /* c가 10진수 숫자인지 ASCII 테이블을 기준으로 문자를 검사합니다. */
    if (c >= 48 && c <= 57)
        return (c); // 여기서 c를 반환합니다. 10진수 숫자인 경우 0이 아닌 값이 됩니다.
    return (0); // 여기에 도달하면 c는 10진수 숫자가 아닙니다.
}
```
{% endcode %}

</details>