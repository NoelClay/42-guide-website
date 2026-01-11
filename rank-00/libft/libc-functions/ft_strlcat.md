# ft\_strlcat

### 주제

{% code overflow="wrap" %}
```
STRLCAT(3) (간소화됨)

NAME
    strlcat -- 크기가 제한된 문자열 연결
SYNOPSIS
    size_t strlcat(char *dst, const char *src, size_t dstsize);
DESCRIPTION
    strlcat() 함수는 snprintf(3)와 동일한 입력 매개변수와 출력 결과를 사용하여 문자열을 연결합니다. 이 함수는 오용되기 쉬운 strncat(3) 함수를 대체하기 위해 더 안전하고, 일관성 있으며, 오류 발생 가능성이 낮은 형태로 설계되었습니다.
    strlcat()은 대상 버퍼의 전체 크기를 취하며 공간이 있다면 NUL-termination을 보장합니다. NUL 문자를 위한 공간이 dstsize에 포함되어야 함을 유의하십시오. 또한, strlcat()은 오직 진정한 'C' 문자열에서만 작동한다는 점을 유의하십시오. 이는 src와 dst 둘 다 반드시 NUL-terminated되어야 함을 의미합니다.
    strlcat()은 문자열 src를 dst의 끝에 추가합니다. 이 함수는 최대 dstsize - strlen(dst) - 1개의 문자를 추가합니다. 그런 다음, dstsize가 0이거나 원래 dst 문자열이 dstsize보다 길지 않은 한 NUL-terminate합니다 (실제로는 dstsize가 잘못되었거나 dst가 올바른 문자열이 아니라는 의미이므로 이런 일은 발생하지 않아야 합니다).
    src와 dst 문자열이 겹치는 경우, 동작은 정의되지 않습니다 (undefined).
RETURN VALUES
    snprintf(3)와 마찬가지로, strlcat() 함수는 생성하려고 시도했던 문자열의 총 길이를 반환합니다. 이는 dst의 초기 길이에 src의 길이를 더한 값입니다.
    반환 값이 dstsize보다 크거나 같다면, 출력 문자열은 잘렸습니다(truncated). 이를 처리하는 것은 호출자의 책임입니다.
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수가 하는 일은 두 문자열을 연결하는 것이라는 점에서 매우 간단하지만, 한 가지 작은 특징이 있습니다. 바로 문자열을 **항상** NUL-terminate한다는 점입니다.

만약 잘림(truncating) 없이 결과로 생성된 연결 문자열을 NUL-terminate하기에 충분히 긴 `dstsize`를 제공하면, `strlcat()`은 `strcat()`을 사용할 때처럼 단순히 두 문자열을 연결합니다. 충분히 긴 `dstsize`를 제공하지 않으면, `dstsize - strlen(dst) - 1`개의 문자를 연결하고 그 뒤에 NUL-terminating 문자를 추가합니다.

`strlcat()` 함수는 문자열을 NUL-terminate하기 위해 문자열을 잘라야 하는 경우에도, 생성하려고 시도했던 문자열의 길이(즉, `dst`의 원래 길이와 `src`의 원래 길이를 더한 값)를 **항상** 반환합니다.

### 힌트

저는 이 함수를 Apple의 C 버전 구현 방식과 동일하게 구현했습니다. 해당 페이지의 링크는 Sources 섹션에서 확인하십시오.

{% code title="ft_strlcat.c" overflow="wrap" lineNumbers="true" %}
```c
size_t	ft_strlcat(char *dst, const char *src, size_t dstsize)
{
    /* src의 원래 길이를 가져옵니다. */
    /* dst의 원래 길이를 가져옵니다. */
    /* 만약 dst의 길이가 dstsize와 같다면 */
    /* 단순히 dst 길이 + src 길이를 반환합니다. */
    /* 만약 dstsize가 src와 dst 모두를 수용할 만큼 충분히 크다면 */
    /* dst의 끝에 src를 연결합니다. */
    /* 그렇지 않다면, 최대 dstsize 문자만큼 연결합니다. */
    return (/* src 길이 + dst 길이 */);
}
```
{% endcode %}

### 주석이 달린 해답

<details>

<summary>ft_strlcat</summary>

{% code title="ft_strlcat.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

size_t	ft_strlcat(char *dst, const char *src, size_t dstsize)
{
    size_t    src_len;
    size_t    dst_len;
    
    /* src와 dst의 원래 길이를 가져옵니다. */
    src_len = ft_strlen(src);
    dst_len = ft_strlen(dst);
    /* strnlen 함수를 구현한다면 이 검사는 생략될 수 있습니다. */
    if (dst_len >= dstsize)
        dst_len = dstsize;
    /* 만약 dst_len이 dst_size와 같다면
     * dst가 이미 최대 문자 수를 포함하고 있으므로 아무것도 연결할 필요가 없음을 의미합니다.
     */
    if (dst_len == dstsize)
        return (dstsize + src_len);
    /* 만약 dstsize가 src와 dst 모두를 수용할 만큼 충분히 크다면 */
    if (src_len < dstsize - dst_len)
    /* 메모리 주소에서 직접 작동하므로, ft_memcpy를 다시 사용합니다.
     * dst의 Pointer를 dst_len만큼 오프셋(offset)하여 dst Pointer가 이제 dst의 끝을 가리키도록 설정합니다.
     * 그런 다음 ft_memcpy에게 최대 src_len + 1개의 문자를 src의 내용으로 복사하도록 지시합니다.
     */
        ft_memcpy(dst + dst_len, src, src_len + 1);
    else
    {
    /* 이 경우, 위와 동일하게 dst Pointer를 dst_len만큼 오프셋하고 src를 그 위치에 복사합니다.
     * 이번에는 dstsize - dst_len - 1개의 문자를 복사합니다.
     */
        ft_memcpy(dst + dst_len, src, dstsize - dst_len - 1);
    /* ft_strlcpy와 마찬가지로, 문자열을 NUL-terminate합니다. */
        dst[dstsize - 1] = '\0';
    }
    /* 마지막으로, src + dst의 원래 길이를 반환합니다. */
    return (dst_len + src_len);
}
```
{% endcode %}

</details>

### 출처

[Apple OpenSource strlcat 구현](https://opensource.apple.com/source/Libc/Libc-997.1.1/string/strlcat.c.auto.html)