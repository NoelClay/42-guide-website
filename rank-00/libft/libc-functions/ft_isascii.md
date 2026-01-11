# ft\_isascii

### 주제

{% code overflow="wrap" %}
```
ISASCII(3) (단순화된 설명)

이름
    isascii -- ASCII 문자를 테스트합니다
개요
    int isascii(int c)
설명
    isascii() 함수는 ASCII 문자를 테스트합니다. 이는 0부터 8진수 0177(포함) 사이의 모든 문자입니다.
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수에 대해서는 man 페이지가 자체적으로 설명하고 있지만, 반환 값이 무엇인지는 명시되어 있지 않습니다.

`isascii()` 함수는 `int` 매개변수로 전달된 문자가 0과 8진수 0177 사이의 ASCII 문자일 경우 0이 아닌 값을 반환합니다. 이는 0과 10진수 127 사이의 문자, 즉 `man ascii` 명령어를 입력할 때 표시되는 모든 문자를 의미합니다.

문자가 0과 8진수 0177 사이의 ASCII 문자가 아닌 경우, `isascii()` 함수는 `0`을 반환합니다.

### 힌트

{% code title="ft_isascii.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_isascii(int c)
{
    if (/* c가 0과 10진수 127 사이인지 확인 */)
        return (/* 원하는 0이 아닌 값 */);
    return (0);
}
```
{% endcode %}

### 주석이 달린 해답

{% hint style="warning" %}
이 함수는 저희가 만들었던 다른 세 함수와 대부분 비슷하게 작동하지만, 약간의 주의 사항이 있습니다.
{% endhint %}

<details>

<summary>ft_isascii</summary>

{% code title="ft_isascii" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_isascii(int c)
{
    /* c가 0과 10진수 127 사이에 있는지 확인합니다 */
    if (c >= 0 && c <= 127)
        return (1);
    /* 이번에는 c를 반환하지 않았다는 점에 유의하십시오 */
    /* 왜냐하면? c의 값이 0인 경우에도 이 함수는 0이 아닌 값을 반환해야 하므로 c를 직접 반환할 수 없기 때문입니다 */
    return (0);
}
```
{% endcode %}

</details>