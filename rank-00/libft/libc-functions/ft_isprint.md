# ft\_isprint

### 주제

{% code overflow="wrap" %}
```
ISPRINT(3) (simplified)

NAME
    isprint -- printing character test (space character inclusive)
SYNOPSIS
    int isprint(int c)
DESCRIPTION
    The isprint() function tests for any printing character, including space. The value of the argument must representable as an an unsigned char or the value of EOF.
RETURN VALUES
    The isprint() function returns zero if the character tests false and returns non-zero if the character tests true.
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수에 대해 man 페이지는 상당히 자명하지만, 추가적인 세부 정보(즉, 출력 가능한 문자가 무엇인지)를 제공하겠습니다.

`isprint()` 함수는 `int` 매개변수로 전달된 문자가 출력 가능한 문자(printing character)일 경우 0이 아닌 값을 반환합니다.

문자가 출력 가능한 문자가 아닐 경우, `isprint()` 함수는 `0`을 반환합니다.

출력 가능한 문자(printing characters)는 10진수 32부터 10진수 126 사이의 모든 문자입니다.

### 힌트

{% code title="ft_isprint.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_isprint(int c)
{
    if (/* c가 32와 126 사이에 있는지 확인합니다 */)
        return (/* 원하는 0이 아닌 값 */);
    return (0);
}
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_isprint</summary>

{% code title="ft_isprint.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_isprint(int c)
{
    /* c가 10진수 32와 10진수 126 사이에 있는지 (포함하여) 확인합니다 */
    if (c >= 32 && c <= 126)
        return (c); // 이 지점에 도달하면 c는 0이 아닌 값이 됩니다.
    return (0);
}
```
{% endcode %}

</details>