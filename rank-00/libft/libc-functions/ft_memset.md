# ft\_memset

### Subject

{% code overflow="wrap" %}
```
MEMSET(3) (simplified)

NAME
    memset -- fill a byte string with a byte value
SYNOPSIS
    void *memset(void *b, int c, size_t len);
DESCRIPTION
    The memset() function writes len bytes of value c (converted to an unsigned char) to the string b.
RETURN VALUES
    The memset() function returns its first argument.
```
{% endcode %}

### Understandable explanation

Man 페이지 설명대로, 이 함수는 값 `c`를 `len` 바이트만큼 문자열 `b`에 기록합니다.

값 `c`는 `unsigned char`로 변환됩니다. 따라서 이 값을 `b` 문자열에 설정하려면, 우리는 `b` 문자열을 `unsigned char` Pointer로 변환해야 합니다. 하지만 반환 값을 기억해야 합니다. 우리는 함수의 첫 번째 매개변수인 `void *b` 문자열을 반환해야 합니다.

그렇다면 원본 매개변수를 변경하지 않고 어떻게 이 매개변수를 변환할 수 있을까요? 임시 변수에 대해 생각해 보십시오.

### Hints

이 함수를 구현하려면, 우리는 임시 변수인 `unsigned char *`를 선언해야 합니다. 그리고 원래의 `void *b` 문자열을 건드리지 않고, 이 Pointer를 통해 모든 조작을 수행할 것입니다.

{% code title="ft_memset.c" overflow="wrap" lineNumbers="true" %}
```c
void    *ft_memset(void *b, int c, size_t len)
{
    /* 임시 unsigned char *를 선언합니다 */
    /* 이 임시 변수를 unsigned char로 변환된 void *b와 같도록 설정합니다 */
    /* len에 도달할 때까지 임시 변수에 대해 반복합니다 */
        /* 해당 반복문 내에서, 현재 byte를 unsigned char로 변환된 c와 같도록 설정합니다 */
    /* void *b를 반환합니다 */
}
```
{% endcode %}

### Commented solution

<details>

<summary>ft_memset</summary>

{% code title="ft_memset.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    *ft_memset(void *b, int c, size_t len)
{
    /* 임시 Pointer를 선언합니다 */
    unsigned char    *tmp_ptr;
    
    /* 임시 Pointer를 unsigned char *로 형변환된 b와 같도록 만듭니다 */
    tmp_ptr = (unsigned char *) b;
    /* len에 도달할 때까지 임시 Pointer에 대해 반복합니다 */
    while (len > 0)
    {
    /* c의 unsigned char 값을 임시 Pointer의 현재 byte에 할당합니다 */
        *(tmp_ptr++) = (unsigned char) c;
        /* len 바이트만 설정하도록 len을 1씩 줄입니다 */
        len--;
    }
    /* 함수의 첫 번째 매개변수인 void *b를 반환합니다 */
    return (b);
}
```
{% endcode %}

</details>
