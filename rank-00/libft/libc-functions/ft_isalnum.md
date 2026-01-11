# ft\_isalnum

### 주제

{% code overflow="wrap" %}
```
ISALNUM(3) (simplified)

NAME
    isalnum -- alphanumeric character test
SYNOPSIS
    int isalnum(int c)
DESCRIPTION
    The isalnum() function tests for any character for which isalpha(3) or isdigit(3) is true. The value of the argument must be representable as an unsigned char or the value of EOF.
RETURN VALUES
    The isalnum() function returns zero if the character tests false and returns non-zero if the character tests true.
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수에 대해서는 man 페이지가 이미 잘 설명하고 있지만, 다른 말로 다시 설명해 드리겠습니다.

`isalnum()` 함수는 `int` 매개변수로 전달된 문자가 알파벳 문자(alphabetical)이거나 10진수 숫자(decimal digit) 문자인 경우 0이 아닌 값을 반환합니다.

문자가 알파벳도 아니고 10진수 숫자 문자도 아닌 경우, `isalnum()` 함수는 `0`을 반환합니다.

### 힌트

{% code title="ft_isalnum.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_isalnum(int c)
{
    if (/* c가 알파벳이거나 c가 숫자인지 확인합니다 */)
        return (/* 원하는 0이 아닌 값 */);
    return (0);
}
```
{% endcode %}

### 주석 처리된 해답

{% hint style="danger" %}
이봐요! 정말 이 함수에 대한 코드가 필요하신가요?
{% endhint %}

<details>

<summary>ft_isalnum (버전 1)</summary>

<pre class="language-c" data-title="ft_isalnum.c" data-overflow="wrap" data-line-numbers><code class="lang-c"><strong>#include "libft.h"
</strong><strong>
</strong><strong>int    ft_isalnum(int c)
</strong>{
    /* 이 검사는 이전에 작성한 두 함수를 활용합니다 */
    if (ft_isalpha(c) || ft_isdigit(c))
        return (c); // 이 지점에 도달했다면 c는 0이 아닌 값이므로 c를 반환할 수 있습니다
    return (0);
}
</code></pre>

</details>

<details>

<summary>ft_isalnum (버전 2)</summary>

{% code title="ft_isalnum.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_isalnum(int c)
{
    /* 이 검사는 버전 1과 동일하지만, 기존 함수를 사용하지 않습니다. 작성하기는 더 길지만, 다른 함수에 의존하는 것을 원치 않을 때 유용할 수 있습니다 */
    if ((c >= 48 && c <= 57) || (c >= 65 && c <= 90) || (c >= 97 && c <= 122))
        return (c); // 이 지점에 도달했다면 c는 0이 아닌 값이므로 단순히 c를 반환할 수 있습니다
    return (0);
}
```
{% endcode %}

</details>