# ft\_strlen

### Subject

{% code overflow="wrap" %}
```
STRLEN(3) (simplified)

NAME
    strlen -- 문자열의 길이를 찾습니다
SYNOPSIS
    size_t(const char *s);
DESCRIPTION
    The strlen() function computes the length of the string s.
RETURN VALUES
    The strlen() function returns the number of characters that precede the terminating NUL character.
```
{% endcode %}

### Understandable explanation

이 함수에 대해서는 man 페이지가 함수의 역할에 대해 상당히 명확하게 설명하고 있지만, 이전에 보지 못했던 새로운 개념이 몇 가지 있습니다.

`strlen()` 함수는 문자열의 종료 `NUL` (`\0`) 문자 이전에 있는 문자들의 개수를 반환합니다.

#### 이것은 무엇을 의미합니까?

만약 문자열이 `abcde\0`라면, `strlen()`은 `5`를 반환할 것입니다.

매개변수로 전달하는 문자열은 앞에 `const` 키워드가 붙어 있습니다. 이는 해당 문자열이 상수 값이기 때문에 함수 내부에서 이 문자열을 수정할 수 없음을 의미합니다.

`strlen()`의 반환 값은 `size_t` 타입입니다. 이것은 무엇일까요? 설명해 드리겠습니다.

[geeksforgeeks.org](https://geeksforgeeks.org/size_t-data-type-c-language)에서 설명하는 바와 같이, `size_t` 데이터 타입은 바이트 단위로 객체의 크기를 나타내는 데 사용되는 타입이며, 따라서 `sizeof` 연산자의 반환 타입으로 사용됩니다. 이는 호스트 시스템이 처리할 수 있는 가장 큰 객체의 크기를 담을 수 있을 만큼 충분히 크다는 것이 보장됩니다. 기본적으로 허용되는 최대 크기는 컴파일러에 따라 달라집니다. 만약 컴파일러가 32비트라면 `unsigned int`의 typedef(즉, 별칭)에 불과하지만, 컴파일러가 64비트라면 `unsigned long long`의 typedef가 됩니다. `size_t` 데이터 타입은 절대 음수가 아닙니다.

### Hints

{% code title="ft_strlen.c" overflow="wrap" lineNumbers="true" %}
```c
size_t    ft_strlen(const char *s)
{
    while( /* \0 문자를 읽지 않는 동안 */)
        /* 카운터를 증가시키고 다음 문자를 읽습니다 */
    return (/* 카운터 */);
}
```
{% endcode %}

### Commented solution

{% hint style="danger" %}
어서요! 당신은 이 함수를 Piscine 기간 동안 수백 번 작성했을 것입니다. 정말로 코드를 봐야 합니까?
{% endhint %}

<details>

<summary>ft_strlen</summary>

{% code title="ft_strlen.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

size_t    ft_strlen(cont char *s)
{
    /* 일반적으로 카운터를 int로 선언하지만, 이 함수에서는
     * size_t가 int보다 크기 때문에 size_t로 선언합니다.
     */
    size_t    i;
    
    i = 0;
    /* 그런 다음 s 배열의 모든 문자를 순회합니다.
     * 이 루프에 종료 조건이 없다고 생각할 수도 있지만,
     * 종료 조건은 존재합니다. 읽는 문자가 \0인 경우 루프
     * 조건이 false로 평가되므로 다시 루프로 진입하지 않습니다.
     */
    while (s[i])
        i++;
    /* 힌트에서 언급했듯이, 카운터를 반환합니다. 이 값은
     * 종료 \0 문자 이전에 있는 문자들의 개수와 같을 것입니다.
     */
    return (i);
}
```
{% endcode %}

</details>
