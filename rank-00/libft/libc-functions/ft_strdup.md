# ft\_strdup

### 주제

{% code overflow="wrap" %}
```
STRDUP(3) (간소화됨)

이름
    strdup -- 문자열의 복사본 저장
요약
    char *strdup(const char *s1);
설명
    strdup() 함수는 문자열 s1의 복사본을 위한 충분한 메모리를 할당하고, 복사 작업을 수행하며, 해당 복사본을 가리키는 Pointer를 반환합니다. 이 Pointer는 이후 free(3) 함수의 인자로 사용될 수 있습니다.
    만약 충분한 메모리를 사용할 수 없다면, NULL이 반환되고 errno는 ENOMEM으로 설정됩니다.
```
{% endcode %}

### 이해하기 쉬운 설명

이 man 페이지는 함수가 수행하는 역할을 매우 명확하게 설명하고 있습니다. 따라서 추가적인 설명이 필요하지 않을 것 같습니다.

### 힌트

이 함수의 반환 값은 `free` 함수를 사용하여 '해제 가능(freeable)'해야 하므로, 우리는 `malloc`을 사용해야 합니다.

{% code title="ft_strdup.c" overflow="wrap" lineNumbers="true" %}
```c
char *ft_strdup(const char *s1)
{
    /* s1을 위한 충분한 공간을 할당하기 위해 malloc을 사용합니다.
     * 우리는 s1 전체를 복사해야 하므로,
     * 이를 위한 충분한 공간이 필요합니다.
     */
     /* s1을 반복하여 방금 할당한 새로운 문자열에 각 문자를 복사합니다.
      */
     /* 할당 및 복사된 문자열을 반환합니다. */
}
```
{% endcode %}

### 주석이 달린 해답

<details>

<summary>ft_strdup</summary>

{% code title="ft_strdup.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_strdup(const char *s1)
{
    char *dest;
    size_t i;
    
    /* s1 + 1 문자에 해당하는 충분한 메모리를 할당합니다.
     * 이는 NUL-terminating character를 위한 공간입니다.
     */
    dest = (char *) malloc(ft_strlen(s1) + 1);
    if (!dest)
        return (NULL);
    i = 0;
    /* 전체 s1 문자열을 반복합니다. */
    while (s1[i])
    {
        /* 현재 s1의 문자를 우리가 위에 할당한 dest 문자열의
         * 같은 위치에 복사합니다.
         */
        dest[i] = s1[i];
        i++;
    }
    /* NUL-terminating character를 설정합니다. */
    dest[i] = 0;
    /* 마지막으로, 새로 생성된 문자열을 반환합니다. */
    return (dest);
}
```
{% endcode %}

</details>