# ft\_substr

### 주제

{% code overflow="wrap" %}
```
FT_SUBSTR (simplified)

NAME
    ft_substr -- 문자열에서 서브스트링 추출
SYNOPSIS
    char *ft_substr(const char *s, unsigned int start, size_t len);
DESCRIPTION
    s 문자열에서 새로운 문자열을 할당(malloc(3) 사용)하여 반환합니다.
    이 새로운 문자열은 'start' 인덱스에서 시작하며 최대 크기는 'len'입니다.
PARAMETERS
    s: 새로운 문자열을 추출할 문자열
    start: 's' 문자열 내에서 새로운 문자열의 시작 인덱스
    len: 새로운 문자열의 최대 크기
RETURN VALUES
    ft_substr()은 새로운 문자열을 반환합니다. 메모리 할당에 실패하면 NULL을 반환합니다.
AUTHORIZED EXTERNAL FUNCTIONS
    malloc(3)
```
{% endcode %}

### 이해하기 쉬운 설명

`ft_substr`은 매개변수로 전달된 문자열 `s`의 서브스트링(substring)을 반환합니다.

다음은 예시입니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
ft_substr("Bonjour comment ca va?", 5, 8);
=> "ur comme"
```
{% endcode %}

### 힌트

먼저, `start` 인덱스가 원본 문자열의 길이보다 큰지 아닌지를 확인해야 합니다.

또한, `start`와 `len`의 합이 전체 문자열의 길이보다 큰지 확인해야 합니다.

그런 다음, 서브스트링을 위한 충분한 메모리를 할당하고, `s[start]`부터 시작하여 `len` 개수만큼의 문자를 새 문자열로 복사합니다.

마지막으로, 서브스트링을 반환하면 됩니다.

### 주석 처리된 해답

<details>

<summary>ft_substr</summary>

{% code title="ft_substr.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_substr(const char *s, unsigned int start, size_t len)
{
    size_t i;
    char *str;
    
    /* 먼저 받은 문자열이 NULL이 아닌지 확인합니다.
     * 만약 NULL이라면, 빈 문자열에서는 서브스트링을 추출할 수 없으므로
     * NULL을 반환합니다.
     */
    if (!s)
        return (NULL);
    /* 만약 start 인덱스가 원본 문자열의 길이보다 크다면,
     * 나중에 해제할 수 있는 (malloc으로 할당된) 빈 문자열을 반환합니다.
     */
    if (start > ft_strlen(s))
        return (ft_strdup(""));
    /* 복사해야 하는 len이 start 인덱스에서 시작하는
     * 문자열의 길이보다 크다면, 이는 문자열 전체를 읽은 후 멈춰야 하며
     * 할당되는 크기가 매개변수로 받은 len과 같을 필요가 없음을 의미합니다.
     * 실제 복사할 크기에 NUL 종료 문자(NUL-terminating character)를 위한
     * 1을 더한 크기만 할당합니다.
     * 이를 통해 실제로 필요한 메모리만 할당할 수 있습니다.
     */
    if (len > ft_strlen(s + start))
        len = ft_strlen(s + start);
    /* 그런 다음 메모리를 할당합니다.
    */
    str = ft_calloc(len + 1, sizeof(char));
    if (!str)
        return (NULL);
    i = 0;
    /* 매개변수로 받은 문자열을 start + i 인덱스부터 반복하면서
     * 각 문자를 새 문자열의 인덱스 i에 복사합니다.
     */
    while (i < len)
    {
        str[i] = s[start + i];
        i++;
    }
    /* 마지막으로 서브스트링을 반환합니다.
     */
    return (str);
}
```
{% endcode %}

</details>