# ft\_memcpy

### 주제

{% code overflow="wrap" %}
```
MEMCPY(3) (simplified)

NAME
    memcpy -- copy memory area
SYNOPSIS
    void *memcpy(void *dst, const void *src, size_t n);
DESCRIPTION
    The memcpy() function copies n bytes from memory area src to memory area dst. If dstt and src overlap, behavior is undefined. Applications in which dst and src might overlap should use memove(3) instead.
RETURN VALUES
    The memcpy() function returns the original value of dst
```
{% endcode %}

### 이해하기 쉬운 설명

`memcpy` 함수는 최대 $n$ 바이트를 `src`에서 `dst`로 복사합니다. man 페이지에서는 메모리 중첩(memory overlapping)에 대해 언급하고 있습니다. 이 내용은 `ft_memmove` 함수 페이지에서 더 자세히 설명해 드리겠습니다.

`memset` 및 `bzero`에서와 마찬가지로, 데이터를 조작하기 위해 임시 Pointer가 필요합니다.

이 함수는 `strcpy` 함수처럼 작동하지만, `memcpy`는 매개변수로 `void *`를 받기 때문에 복사하려는 모든 유형의 Pointer를 전달할 수 있다는 차이점이 있습니다.

### 힌트

{% code title="ft_memcpy.c" overflow="wrap" lineNumbers="true" %}
```c
void    *ft_memcpy(void *dst, const void *src, size_t n)
{
    /* dst를 위한 임시 Pointer를 선언합니다 */
    /* src를 위한 임시 Pointer를 선언합니다 */
    
    /* src와 dst가 NULL이면, dst를 반환합니다 */
    /* dst 임시 Pointer를 unsigned char *로 변환된 dst와 같게 만듭니다 */
    /* src 임시 Pointer를 unsigned char *로 변환된 src와 같게 만듭니다 */
    /* n에 도달할 때까지 dst 임시 Pointer를 순회합니다 */
        /* dst 임시 Pointer의 현재 바이트를 src 임시 Pointer의 현재 바이트와 같게 설정합니다 */
        /* dst를 반환합니다 */
}
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_memcpy</summary>

{% code title="ft_memcpy.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    *ft_memcpy(void *dst, const void *src, size_t n)
{
    /* 두 임시 Pointer를 선언합니다 */
    unsigned char    *tmp_dst;
    unsigned char    *tmp_src;
    
    /* dst와 src가 모두 NULL Pointer인 경우, 아무 작업도 수행하지 않으므로
     * dst를 즉시 반환할 수 있습니다.
     */
    if (dst == (void *)0 && src == (void *)0)
        return (dst);
    /* 두 임시 Pointer에 "실제" Pointer의 변환된 값을
     * 할당합니다.
     */
    tmp_dst = (unsigned char *) dst;
    tmp_src = (unsigned char *) src;
    /* n 바이트에 도달할 때까지 두 임시 Pointer를 순회합니다.
     */
    while (n > 0)
    {
        /* tmp_dst의 현재 바이트를 tmp_src의 현재 바이트와
         * 같게 만듭니다.
         */
        *(tmp_dst++) = *(tmp_src++);
        /* n 바이트만 복사하도록 n을 1씩 줄입니다 */
        n--;
    }
    /* 임시 Pointer가 아닌 원래의 dst를 반환합니다 */
    return (dst);
}
```
{% endcode %}

</details>