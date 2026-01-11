# ft\_bzero

### 주제 (Subject)

{% code overflow="wrap" %}
```
BZERO(3) (simplified)

NAME
    bzero -- write zeroes to a bye string
SYNOPSIS
    void bzero(void *s, size_t n);
DESCRIPTION
    The bzero() function writes n zeroed bytes to the string s. If n is zero, bzero() does nothing.
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

이 함수는 `memset()` 함수와 동일한 방식으로 작동합니다. 다만, 어떤 문자를 작성할지 지정할 필요가 없으며, 항상 `0` (`NUL` 문자)으로 설정됩니다.

이 함수는 아무것도 반환하지 않으며, `size_t n`으로 전달한 작성할 문자 수가 `0`인 경우 `bzero`는 아무 작업도 수행하지 않습니다.

### 힌트 (Hints)

{% code title="ft_bzero.c" overflow="wrap" lineNumbers="true" %}
```c
void    ft_bzero(void *s, size_t n)
{
    /* 임시 포인터를 선언합니다. */
    /* 임시 포인터를 char * 타입으로 변환된 *s와 같게 만듭니다. */
    /* n개의 문자에 도달할 때까지 임시 포인터에서 반복합니다. */
        /* 해당 반복문 내에서 현재 바이트를 0으로 설정합니다. */
}
```
{% endcode %}

### 주석이 달린 해답 (Commented solution)

<details>

<summary>ft_bzero</summary>

{% code title="ft_bzero.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    ft_bzero(void *s, size_t n)
{
    /* 임시 포인터를 선언합니다. */
    char    *tmp_ptr;
    
    /* 임시 포인터를 char * 타입으로 변환된 s와 같게 만듭니다. */
    tmp_ptr = (char *) s;
    /* n에 도달할 때까지 임시 포인터에서 반복합니다. */
    while (n > 0)
    {
        /* 임시 포인터의 현재 바이트에 0을 할당합니다. */
        *(tmp_ptr++) = 0;
        /* n 바이트만 0으로 설정하도록 n을 1씩 줄입니다. */
         n--;
    }
}
```
{% endcode %}

</details>