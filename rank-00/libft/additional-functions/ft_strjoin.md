# ft\_strjoin

### 주제

{% code overflow="wrap" %}
```
FT_STRJOIN (간략화)

NAME
    ft_strjoin -- 2개의 string을 새로운 string으로 연결합니다.
SYNOPSIS
    char *ft_strjoin(const char *s1, const char *s2);
DESCRIPTION
    s1과 s2의 연결 결과로 생성된 새로운 string을 (malloc(3)을 사용하여) 할당하고 반환합니다.
PARAMETERS
    s1: 접두사 string (prefix string)
    s2: 접미사 string (suffix string)
RETURN VALUES
    ft_strjoin()은 새로운 string을 반환하며, 메모리 할당이 실패하면 NULL을 반환합니다.
AUTHORIZED EXTERNAL FUNCTIONS
    malloc(3)
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 기본적으로 `ft_strlcat`와 유사하게 작동합니다. 하지만 `ft_strlcat`에 매개변수로 올바르게 할당되어야 하는 `destination` `string`을 전달하는 대신, `ft_strjoin`은 두 개의 `string`만 전달받으며, 두 string에 필요한 메모리와 NUL-terminating character를 위한 메모리를 직접 할당합니다.

결과 string에서 `s1`이 첫 번째 string이 되며, `s2`가 두 번째 string이 됩니다.

### 힌트

두 string 모두에 충분한 메모리를 할당할 수 있도록 두 string의 길이를 얻어야 합니다.

이것이 가장 먼저 해야 할 일입니다. 그런 다음, 두 string의 길이와 NUL-terminating character를 위한 충분한 메모리를 할당합니다.

그 후, 새로 할당된 string에 `s1`을 복사하고, 이어서 `s2`를 복사합니다. 마지막으로, 마지막 문자를 `NUL`로 설정합니다.

### 주석이 달린 해결책

<details>

<summary>ft_strjoin</summary>

{% code title="ft_strjoin.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_strjoin(const char *s1, const char *s2)
{
    char *res;
    int i;
    int j;
    
    i = 0;
    j = 0;
    /* 필요한 메모리를 할당합니다 */
    res = (char *) malloc((ft_strlen(s1) + ft_strlen(s2) + 1) * sizeof(char));
    if (!res)
        return (NULL);
    /* s1을 결과 res string으로 복사합니다 */
    while (s1[i])
        res[j++] = s1[i++];
    /* i를 0으로 재설정해야 합니다. 그렇지 않으면 s2를
     * 처음부터 복사할 수 없습니다
     */
    i = 0;
    /* s2를 결과 res string으로 복사합니다 */
    while (s2[i])
        res[j++] = s2[i++];
    /* !! string을 NUL-terminate하는 것을 잊지 마세요 !! */
    res[j] = 0;
    /* 마지막으로, 새로운 string을 반환합니다 */
    return (res);
}
```
{% endcode %}

</details>