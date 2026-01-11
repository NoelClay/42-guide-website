# ft\_strnstr

### 주제

{% code overflow="wrap" %}
```
STRNSTR(3) (간소화)

이름
    strnstr -- 문자열에서 부분 문자열의 위치를 찾습니다.
개요
    char *strnstr(const char *haystack, const char *needle, size_t len);
설명
    strnstr() 함수는 문자열 haystack에서 널(null)로 끝나는 문자열 needle이 처음으로 나타나는 위치를 찾습니다. 이 검색은 최대 len 개의 문자 내에서 수행됩니다.
    '\0' 문자 뒤에 나타나는 문자들은 검색되지 않습니다.
반환 값
    needle이 빈 문자열인 경우, haystack이 반환됩니다. needle이 haystack 내에 전혀 존재하지 않는 경우, NULL이 반환됩니다. 그 외의 경우, needle이 처음으로 나타나는 위치의 첫 문자(first character)에 대한 Pointer가 반환됩니다.
```
{% endcode %}

### 이해하기 쉬운 설명

`strnstr()` 함수는 `strchr()`와 동일한 방식으로 작동하지만, 단일 문자 대신 최대 `n`개의 문자 범위 내에서 완전한 부분 문자열(substring)을 검색합니다.

### 힌트

{% code title="ft_strnstr.c" overflow="wrap" lineNumbers="true" %}
```c
char *ft_strnst(const char *haystack, const char *needle, size_t len)
{
    /* needle이 비어있는지 확인합니다. */
        /* haystack을 반환합니다. */
    /* haystack을 순회합니다. */
        /* haystack의 현재 문자가 needle의 해당 문자와 같을 동안 */
             /* 완전한 needle을 찾았는지 확인합니다. */
}
```
{% endcode %}

두 개의 다른 카운터(counter)가 필요하다는 점도 알려드릴 수 있습니다.

### 주석이 달린 해답

<details>

<summary>ft_strnstr</summary>

{% code title="ft_strnstr.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_strnstr(const char *haystack, const char *needle, size_t len)
{
    size_t i;
    size_t j;
    
    i = 0;
    j = 0;
    /* needle이 비어있는지 확인합니다. */
    if (needle[0] == 0)
        return ((char *) haystack);
    /* haystack 전체를 순회하지 않았거나 i = len에 도달할 때까지 haystack을 순회합니다. */
    while (haystack[i] && i < len)
    {
        /* 이 첫 번째 while 루프 내부에서
         * 현재 문자가 needle의 올바른 문자인 한 
         * haystack을 순회합니다.
         */
        while (haystack[i + j] == needle[j] && haystack[i + j] && i + j < len)
        {
            /* 현재 haystack 문자가 needle에서 올바른 문자인 동안 이 루프 내부에 있습니다.
             * 따라서 j를 증가시켜 haystack의 오프셋을 조정합니다.
             */
            j++;
            /* needle[j] == 0 이면, 전체 needle을 모두 읽었다는 의미이며,
             * haystack에서 needle을 찾았다는 뜻입니다.
             * needle의 첫 번째 문자에 대한 Pointer를 반환할 수 있습니다.
             * 이것이 2개의 카운터를 사용한 이유입니다. 이제 haystack Pointer를 
             * needle의 첫 번째 위치인 i만큼 오프셋할 수 있습니다.
             */
            if (needle[j] == 0)
                return ((char *) haystack + i);
        }
        i++;
        /* j를 다시 0으로 설정하는 것을 잊지 마십시오. 그렇지 않으면
         * 두 번째 haystack 문자부터 needle 내에서 오프셋된 상태로 시작하게 됩니다.
         */
        j = 0;
    }
    return (0);
}
```

</details>