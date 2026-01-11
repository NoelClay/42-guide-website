# ft\_memchr

### 주제

{% code overflow="wrap" %}
```
MEMCHR(3) (simplified)

NAME
    memchr -- byte 문자열 내에서 바이트를 찾습니다.
SYNOPSIS
    void *memchr(const void *s, int c, size_t n);
DESCRIPTION
    memchr() 함수는 문자열 s 내에서 c (unsigned char로 변환됨)의 첫 번째 출현 위치를 찾습니다.
RETURN VALUES
    memchr() 함수는 해당 바이트를 가리키는 Pointer를 반환하거나, n 바이트 내에 그러한 바이트가 존재하지 않는 경우 NULL을 반환합니다.
```
{% endcode %}

### 이해하기 쉬운 설명

`memchr()` 함수는 `strchr()` 함수와 유사하게 작동합니다. 차이점은 `strchr()`이 '문자열'(`char *`)을 대상으로 하는 반면, `memchr()`은 byte string (`void *`)을 대상으로 작동한다는 점입니다.

이는 우리가 원하는 모든 종류의 데이터를 `memchr()`에 보낼 수 있으며, 여전히 작동할 것임을 의미합니다.

`memchr()`은 또한 세 번째 매개변수인 `n`을 가집니다. 이 매개변수는 함수에게 검색할 바이트 수를 알려줍니다. 이 매개변수가 필요한 이유는 `s`가 '문자열'이 아니기 때문에 NUL-terminating character를 가지고 있지 않기 때문입니다. 만약 이 매개변수가 없다면, 우리는 매번 임의의 수의 바이트를 읽게 될 것입니다.

### 힌트

{% code title="ft_memchr.c" overflow="wrap" lineNumbers="true" %}
```c
void *ft_memchr(const void *s, int c, size_t n)
{
    /* man 페이지에서 언급했듯이, 검색은 unsigned char로 변환된 c에
     * 대해 수행되므로, 우리는 c와 s 모두를 unsigned char로 변환해야 합니다.
     */
    /* 카운터가 n과 같아질 때까지 byte string을 반복합니다. */
    /* 현재 바이트를 c와 비교합니다. */
        /* 만약 두 값이 같다면, 이 바이트의 주소를 void *로 반환합니다.
         */
    /* 만약 n 바이트를 검색했지만 찾고 있던 것을 발견하지 못했다면
     * NULL을 반환합니다.
     */
    /* 보시다시피, 이 함수는 strchr 및 strrchr 함수와 매우 유사하므로,
     * 솔루션을 보기 전에 이 함수들을 살펴보십시오.
     */
}
```
{% endcode %}

### 주석 처리된 솔루션

<details>

<summary>ft_memchr.c</summary>

{% code title="ft_memchr.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void *ft_memchr(const void *s, int c, size_t n)
{
    unsigned char *str;
    size_t i;
    unsigned char uc;
    
    /* s와 c 모두를 unsigned char로 변환합니다. */
    str = (unsigned char *) s;
    uc (unsigned char) c;
    i = 0;
    /* n 바이트만큼 반복합니다. */
    while (i < n)
    {
        /* strchr과 동일한 검사입니다. */
        if (str[i] == uc)
            /* 여기서는 strchr에서 반환했던 char Pointer 대신
             * void Pointer를 반환합니다.
             */
            return ((void *) &str[i]);
        i++;
    }
    /* 이 지점에 도달했다면, n 바이트 내에서 c의 출현을 찾지 못한 것이므로,
     * NULL을 반환합니다.
     */
    return (NULL);
}
```
{% endcode %}

</details>