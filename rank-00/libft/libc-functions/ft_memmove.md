# ft\_memmove

### 주제

{% code overflow="wrap" %}
```c
MEMMOVE(3) (간소화)

NAME
    memmove -- 바이트 문자열 복사
SYNOPSIS
    void *memmove(void *dst, const void *src, size_t len);
DESCRIPTION
    memmove() 함수는 src 문자열에서 dst 문자열로 len 바이트를 복사합니다.
    두 문자열은 겹칠 수 있으며, 복사는 항상 비파괴적인(non-destructive) 방식으로 수행됩니다.
RETURN VALUES
    memmove() 함수는 dst의 원래 값을 반환합니다.
```
{% endcode %}

### 이해하기 쉬운 설명

`memmove()` 함수는 `memcpy()` 함수와 동일하게 동작하지만, 이번에는 매뉴얼에서 언급된 대로 비파괴적인(non-destructive) 방식으로 복사가 이루어집니다. 이는 두 문자열(src와 dst)이 메모리에서 겹칠(overlap) 수 있으며, 복사를 수행할 때 이 함수가 문자열의 일부 또는 전체를 덮어쓰지(overwrite) 않음을 의미합니다.

#### 메모리 겹침(Memory Overlapping)이란 무엇입니까?

[stackexchange.com](https://cs50.stackexchange.com/questions/14615/memory-overlap-in-c)에서 매우 좋은 설명을 찾았으므로, 메모리 겹침이 무엇인지 설명하기 위해 여기에 복사합니다.

> 각 char가 1 바이트 길이인 5개의 char 배열이 있다고 가정해 보겠습니다.
>
> ```bash
> +++++++++++++++++++++++++++++++
> | 'a' | 'b' | 'c' | 'd' | 'e' |
> +++++++++++++++++++++++++++++++
>  0x100 0x101 0x102 0x103 0x104
> ```
>
> 이제 `memcpy`의 man 페이지에 따르면, 이 함수는 3개의 인자, 즉 대상(destination) 메모리 블록에 대한 Pointer, 원본(source) 메모리 블록에 대한 Pointer, 그리고 복사할 바이트 크기(`size_t`)를 받습니다.
>
> 만약 대상이 `0x102`, 원본이 `0x100`, 크기가 `3`이라면 어떻게 될까요?
> 여기서 메모리 겹침(Memory overlapping)이 발생합니다. 즉, `0x100`이 `0x102`로 복사되고, `0x101`이 `0x103`으로 복사되며, `0x102`가 `0x104`로 복사됩니다.
>
> 저희는 먼저 `0x102`로 복사한 다음, `0x102`에서 복사한다는 점에 주목하십시오. 이는 원래 `0x102`에 있던 값이 손실되었음을 의미하는데, 그 이유는 `0x102`에서 복사하기 전에 복사해 넣은 값으로 덮어썼기 때문입니다. 따라서 결과는 다음과 같을 것입니다.
>
> ```bash
> +++++++++++++++++++++++++++++++
> | 'a' | 'b' | 'a' | 'b' | 'a' |
> +++++++++++++++++++++++++++++++
>  0x100 0x101 0x102 0x103 0x104
> ```
>
> 다음 대신에요.
>
> ```bash
> +++++++++++++++++++++++++++++++
> | 'a' | 'b' | 'a' | 'b' | 'c' |
> +++++++++++++++++++++++++++++++
>  0x100 0x101 0x102 0x103 0x104
> ```
>
> `memmove`와 같은 함수는 메모리 겹침을 어떻게 처리할까요?
>
> `memmove`의 man 페이지에 따르면, 이 함수는 원본 블록에서 대상 블록으로 직접 복사하는 `memcpy`와 같은 함수와 달리, 복사할 바이트를 임시 배열에 먼저 복사한 다음 대상 블록에 붙여넣습니다.

이것이 메모리 겹침에 대한 설명입니다. 또한 `memmove`는 복사할 바이트를 임시 배열에 복사한 다음 대상 블록에 붙여넣는다고 설명되어 있습니다. 하지만 LIBFT의 주제에서 언급된 대로 이 함수에서는 `malloc()`을 사용할 수 없기 때문에 저는 이 방식을 사용하지 않았습니다. `malloc()`을 사용하지 않고 제가 구현한 방식은 먼저 두 메모리 블록이 겹치는지 아닌지를 확인하는 것입니다. 만약 겹치는 경우, 원본 메모리 블록의 끝에서 시작 지점까지 복사합니다. 겹치지 않는 경우, 시작부터 끝까지 "일반적으로" 복사합니다.

### 힌트

ft\_memmove

{% code title="ft_memmove.c" overflow="wrap" lineNumbers="true" %}
```c
void    *ft_memmove(void *dst, const void *src, size_t len)
{
    /* src와 dst를 위한 임시 포인터 2개를 선언합니다 */
    /* 카운터를 선언합니다 */
    /* src와 dst가 모두 NULL인지 확인합니다 */
    /* dst 임시 포인터를 char *로 변환된 dst와 같게 만듭니다 */
    /* src 임시 포인터를 char *로 변환된 src와 같게 만듭니다 */
    /* src와 dst가 겹치는 경우 */
        /* len이 0보다 큰 동안 반복하여 src를 dst로 복사합니다 */
    /* src와 dst가 겹치지 않는 경우 */
        /* 카운터가 len보다 작은 동안 반복하여 src를 dst로 복사합니다 */
    /* dst를 반환합니다 */
}
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_memmove</summary>

{% code title="ft_memmove.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    *ft_memmove(void *dst, const void *src, size_t len)
{
    /* 2개의 임시 포인터와 카운터를 선언합니다 */
    char    *c_src;
    char    *c_dst;
    size_t    i;
    
    /* src와 dst 둘 다 NULL인 경우, 즉시 NULL을 반환합니다 */
    if (!dst && !src)
        return (NULL);
    /* 원본 값을 건드리지 않도록 src와 dst의 변환된 값을 임시 포인터에 할당합니다 */
    c_src = (char *) src;
    c_dst = (char *) dst;
    i = 0;
    /* 대상(destination)의 주소가 원본(source)의 주소보다 큰지 확인합니다. 
     * 이 경우, 끝에서 시작 지점으로 복사합니다.
     */ 
    if (c_dst > c_src)
        while (len-- > 0)
            c_dst[len] = c_src[len];
    /* 대상의 주소가 원본의 주소보다 크지 않은 경우, 
     * 평소처럼 시작부터 끝까지 복사합니다.
     */
    else
    {
        while (i++ < len)
            c_dst[i] = c_src[i];
    }
    return (dst);
}
```
{% endcode %}

</details>
