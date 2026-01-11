# ft\_memcmp

### 주제

{% code overflow="wrap" %}
```
MEMCMP(3) (simplified)

NAME
    memcmp -- compare byte string
SYNOPSIS
    int memcmp(const void *s1, const void *s2, size_t n)
DESCRIPTION
    The memcmp() function compares byte string s1 against byte string s2.
    Both strings are assumed to be n bytes long.
RETURN VALUES
    The memcmp() function returns zero if the two strings are identical, otherwise returns the difference betwee the first two differing bytes (treated as unsigned char values, so that '\200' is greater than '\0', for example).
    Zero-length strings are always identical. This behaviour is not required by C and portable code should only depend on the sign of the returned value.
```
{% endcode %}

### 이해하기 쉬운 설명

`memcmp()` 함수는 byte string을 비교합니다. 이 함수는 `strncmp()` 함수와 유사하게 작동합니다.

여기서의 차이점은 `memcmp()`가 byte string을 다루기 때문에 매개변수로 void Pointer를 사용하며, 세 번째 매개변수(`n`)는 man 페이지에 명시된 것처럼 두 string의 가정된 길이를 나타냅니다. 이는 `memcmp()`가 `n` 바이트를 초과하여 비교하지 않음을 의미합니다.

반환 값은 발견된 차이에 따라 달라집니다.

두 string 사이에 차이가 없다면, 반환 값은 0이 됩니다.

차이가 발견되었고, `s1`의 첫 번째 다른 바이트가 `s2`의 같은 위치에 있는 바이트보다 작다면 (즉, `s1 - s2 < 0` 이라면), 반환 값은 음수가 됩니다.

차이가 발견되었고, `s1`의 첫 번째 다른 바이트가 `s2`의 같은 위치에 있는 바이트보다 크다면 (즉, `s1 - s2 > 0` 이라면), 반환 값은 양수가 됩니다.

### 힌트

{% code title="ft_memcmp.c" overflow="wrap" lineNumbers="true" %}
```c
int ft_memcmp(const void *s1, const void *s2, size_t n)
{
    /* n 바이트에 도달할 때까지 두 string을 순회합니다 */
    /* 현재 s1의 바이트가 현재
     * s2의 바이트와 다른지 확인합니다.
     */
           /* 바이트가 다르다면, 두 문자 사이의 차이를
            * 반환합니다.
            */
     /* n 바이트까지 두 byte string을 모두 읽었고 차이가
      * 발견되지 않았다면, 차이가 없으므로 0을 반환합니다.
      */h2
}
```
{% endcode %}

## 주석 달린 솔루션

<details>

<summary>ft_memcmp</summary>

{% code title="ft_memcmp.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int ft_memcmp(const void *s1, const void *s2, size_t n)
{
    unsigned char *str1;
    unsigned char *str2;
    size_t i;
 
    /* s1과 s2를 unsigned char로 변환합니다 */   
    str1 = (unsigned char) *s1;
    str2 = (unsigned char) *s2;
    i = 0;
    /* strcmp와 동일한 루프입니다 */
    while (i < n)
    {
       /* 두 string에서 현재 바이트가 다른지 확인합니다 */
        if ((unsigned char) str1[i] != (unsigned char) str2[i])
            /* 두 문자 사이의 차이를 반환합니다 */
            return ((unsigned char) str1[i] - (unsigned char) str2[i]);
    }
    /* 만약 두 string 전체를 읽었고 차이가 없었다면,
     * 0을 반환합니다.
     */
    return (0);
}
```
{% endcode %}

</details>