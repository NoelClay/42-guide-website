# ft\_tolower

### 주제

{% code overflow="wrap" %}
```
TOLOWER(3) (simplified)

NAME
    tolower -- upper case to lower case letter conversion
SYNOPSIS
    int tolower(int c);
DESCRIPTION
    The tolower() function converts an upper-case letter to the corresponding lower-case letter. The argument must be representable as an unsigned char or the value of EOF.
RETURN VALUES
    If the argument is an upper-case letter, the tolower() function returns the corresponding lower-case letter if there is one; otherwise, the argument is returned unchanged.
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수가 수행하는 작업에 대해 더 자세히 설명할 필요는 없을 것 같습니다. 매뉴얼 페이지(man)가 이 부분에 대해서는 매우 자명하게 설명하고 있습니다.

### 힌트

{% code title="ft_tolower.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_tolower(int c)
{
    if (/* c is an upper-case letter */)
        return (/* corresponding lower-case letter */);
    return (c);
}
```
{% endcode %}

### 주석이 달린 해답

<details>

<summary>ft_tolower</summary>

{% code title="ft_tolower.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_tolower(int c)
{
    /* 이 부분은 해당 문자가 십진수 ASCII 값(65 => A; 90 => Z)을 기준으로 
     * 대문자인지 확인합니다.
     */
    if (c >= 65 && c <= 90)
        /* ASCII 테이블에서 대문자는 소문자보다 인덱스가 32만큼 작습니다. 
         * 따라서 해당하는 소문자를 얻으려면 대문자에 32를 더합니다.
         */
        return (c + 32);
    /* 매뉴얼 페이지에 설명된 바와 같이, 해당 문자가 대문자가 아닌 경우 
     * 인수는 변경되지 않고 그대로 반환됩니다. 따라서 c를 바로 반환합니다.
     */
    return (c);
}
```
{% endcode %}

</details>
```