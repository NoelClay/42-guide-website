# ft\_toupper

### 주제 (Subject)

{% code overflow="wrap" %}
```
TOUPPER(3) (간소화됨)

NAME
    toupper -- 소문자를 대문자로 변환
SYNOPSIS
    int toupper(int c);
DESCRIPTION
    toupper() 함수는 소문자를 해당하는 대문자로 변환합니다. 인자는 unsigned char로 표현 가능하거나 EOF 값이어야 합니다.
RETURN VALUES
    인자가 소문자인 경우, toupper() 함수는 해당하는 대문자가 있다면 그것을 반환합니다. 그렇지 않은 경우, 인자는 변경되지 않고 그대로 반환됩니다.
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

이 함수가 무엇을 하는지에 대해 더 자세히 설명할 필요는 없을 것 같습니다. man 페이지가 이 기능에 대해 충분히 자명하게 설명하고 있습니다.

### 힌트 (Hints)

{% code title="ft_toupper.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_toupper(int c)
{
    if (/* c가 소문자인 경우 */)
        return (/* 해당하는 대문자 */);
    return (c);
}
```
{% endcode %}

### 주석이 달린 해결책 (Commented solution)

<details>

<summary>ft_toupper</summary>

{% code title="ft_toupper.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_toupper(int c)
{
    /* 이는 10진수 ASCII 값(97 => a; 122 => z)을 사용하여
     * 해당 문자가 소문자인지 확인합니다.
     */
    if (c >= 97 && c <= 122)
        /* ASCII 테이블에서 대문자는 소문자보다 32만큼
         * 낮은 인덱스를 가집니다. 따라서 해당하는 대문자를 얻으려면
         * 소문자에서 32를 뺍니다.
         */
        return (c - 32);
    /* man 페이지에 명시된 바와 같이, 해당 문자가 소문자가 아닌 경우
     * 인자는 변경되지 않고 그대로 반환됩니다. 따라서
     * c를 직접 반환합니다.
     */
    return (c);
}
```
{% endcode %}

</details>
```