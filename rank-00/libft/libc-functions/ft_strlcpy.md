# ft\_strlcpy

### 주제 (Subject)

{% code overflow="wrap" %}
```
STRLCPY(3) (간소화)

NAME
    strlcpy -- 크기 제한이 있는 문자열 복사
SYNOPSIS
    size_t strlcpy(char *dst, const char *src, size_t dstsize);
DESCRIPTION
    strlcpy() 함수는 snprintf(3)와 동일한 입력 매개변수와 출력 결과를 사용하여 문자열을 복사합니다. 이 함수는 오용되기 쉬운 strncpy(3) 함수를 대체하기 위해 더 안전하고, 일관성 있으며, 오류 발생 가능성이 낮은 형태로 설계되었습니다.
    strlcpy()는 대상 버퍼의 전체 크기를 받고 공간이 충분하다면 NUL-termination을 보장합니다. NUL 문자를 위한 공간이 dstsize에 포함되어야 함을 유의하십시오. 또한, strlcpy()는 진정한 ''C'' 문자열에서만 작동한다는 점을 유의하십시오. 이는 strlcpy()에게 있어 src는 반드시 NUL-terminated되어야 함을 의미합니다.
    strlcpy()는 문자열 src에서 dst로 최대 dstsize - 1개의 문자를 복사하며, dstsize가 0이 아닐 경우 결과에 NUL-termination을 추가합니다.
    만약 src와 dst 문자열이 겹치면(overlap), 동작은 정의되지 않습니다(undefined).
RETURN VALUES
    strlcpy() 함수는 생성하려고 시도했던 문자열의 전체 길이, 즉 src의 길이를 반환합니다.
    만약 반환 값이 >= dstsize라면, 출력 문자열은 잘렸습니다(truncated).
    이를 처리하는 것은 호출자(caller)의 책임입니다.
```
{% endcode %}

### 쉽게 이해하기 위한 설명 (Understandable explanation)

이 함수가 하는 일은 매우 간단합니다. 하나의 문자열을 다른 문자열로 복사하지만, 작은 특징이 있습니다. 바로 문자열을 **항상** NUL-terminate 한다는 것입니다.

만약 문자열을 자르지 않고 NUL-terminate하기에 충분히 긴 `dstsize`를 제공한다면, `strlcpy()`는 여러분이 `strcpy()`를 사용할 때처럼 단순히 문자열을 복사할 것입니다. 만약 충분히 긴 `dstsize`를 제공하지 않는다면, 소스에서 대상까지 `dstsize - 1`개의 문자를 복사하고, 그 뒤에 NUL-terminating 문자를 추가합니다.

`strlcpy()` 함수는 **항상** 생성하려고 시도했던 문자열의 길이, 즉 `src`의 길이를 반환합니다. 비록 문자열을 NUL-terminate하기 위해 잘라내야 하는 경우라도 마찬가지입니다.

### 힌트 (Hints)

저는 이 함수를 Apple의 C 버전 구현 방식과 동일하게 구현했습니다. 소스(Sources) 섹션에서 해당 페이지 링크를 확인하십시오.

{% code title="ft_strlcpy.c" overflow="wrap" lineNumbers="true" %}
```c
size_t	ft_strlcpy(char *dst, const char *src, size_t dstsize)
{
    /* src의 길이를 가져옵니다 */
    /* dstsize가 src 길이와 NUL 문자를 담을 만큼 충분히 큰지 확인합니다 */
    /* 전체 src를 dst로 복사합니다 */
    /* 그렇지 않다면(else) */
    /* dstsize - 1개의 문자를 dst로 복사합니다 */
    return (/* src의 길이 */);
}
```
{% endcode %}

### 주석이 달린 해답 (Commented solution)

<details>

<summary>ft_strlcpy</summary>

{% code title="ft_strlcpy.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

size_t    ft_strlcpy(char *dst, const char *src, size_t dstsize)
{
    size_t    src_len;
    
    /* 우리의 ft_strlen 함수를 사용하여 src의 길이를 가져옵니다 */
    src_len = ft_strlen(src);
    /* src_len과 종료 NUL 문자를 담을 만큼 dstsize가 충분히 큰지 확인합니다 */
    if (src_len + 1 < dstsize)
        /* ft_memcpy를 사용하여 소스를 대상에 복사합니다 */
        ft_memcpy(dst, src, src_len + 1);
    /* 만약 dstsize가 충분히 크지 않다면, 문자열을 복사할 때 잘라내야 합니다 */
    /* 또한 dstsize가 0인지 확인합니다. 만약 0이라면, 복사할 필요가 없으므로 */
    /* 이 조건문을 건너뛰게 됩니다 */
    else if (dstsize != 0)
    {
        /* ft_memcpy를 다시 사용하지만, 최대 크기로 src_len + 1 대신 */
        /* dstsize - 1을 제공합니다 */
        ft_memcpy(dst, src, dstsize - 1);
        /* 그런 다음 문자열을 NUL-terminate 합니다 */
        dst[dstsize - 1] = 0;
    }
    /* 마지막으로, src의 원래 길이를 반환합니다 */
    return (src_len);
}
```
{% endcode %}

</details>

### 출처 (Sources)

[Apple OpenSource strlcpy 구현](https://opensource.apple.com/source/xnu/xnu-4570.1.46/osfmk/arm/strlcpy.c.auto.html)