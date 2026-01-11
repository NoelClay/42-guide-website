# ft\_strchr

### Subject

{% code overflow="wrap" %}
```
STRCHR(3) (간소화됨)

NAME
    strchr -- 문자열 내에서 문자 찾기
SYNOPSIS
    char *strchr(const char *s, int c);
DESCRIPTION
    strchr() 함수는 s가 가리키는 문자열 내에서 c (char로 변환됨)의 첫 번째 발생 위치를 찾습니다. 종료 NULL 문자는 문자열의 일부로 간주됩니다. 따라서 c가 '\0'인 경우, 함수는 종료 '\0' 문자를 찾습니다.
RETURN VALUES
    strchr() 함수는 찾은 문자에 대한 Pointer를 반환하며, 문자열 내에 문자가 존재하지 않으면 NULL을 반환합니다.
```
{% endcode %}

### Understandable explanation

`strchr()` 함수는 문자열 내에서 하나의 문자를 검색합니다. 해당 문자를 찾으면, 이 특정 문자의 첫 번째 발생 위치에 대한 Pointer를 반환합니다.

이 문자가 문자열 내에 존재하지 않는다면, `NULL`을 반환합니다.

또한, 찾으려는 문자가 `\0`인 경우에도 해당 문자에 대한 Pointer를 반환해야 합니다.

### Hints

{% code title="ft_strchr.c" overflow="wrap" lineNumbers="true" %}
```c
char *ft_strchr(const char *s, int c)
{
    /* 전체 문자열을 반복합니다. */
    /* 현재 문자가 찾아야 하는 문자와 같은지 확인합니다. */
    /* 전체 문자열을 반복한 후, 찾아야 하는 문자가 '\0'인 경우를 대비하여 해당 문자를 다시 확인합니다.
     */
    /* 문자열 내에서 c를 찾지 못했다면, NULL을 반환합니다. */
}
```
{% endcode %}

### Commented solution

<details>

<summary>ft_strchr</summary>

{% code title="ft_strchr.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_strchr(const char *s, int c)
{
    unsigned int    i;
    char            cc,
    
    /* c를 int로 받았으므로 char로 변환합니다. */
    cc = (char) c;
    i = 0;
    /* 전체 문자열 s를 반복합니다. */
    while (s[i])
    {
        /* 현재 문자가 cc와 같다면 
         * 이는 문자열 내에서 c의 발생 위치를 찾았다는 의미입니다.
         * 따라서, 해당 char의 주소를 char Pointer로 반환합니다.
         */
        if (s[i] == cc)
            return ((char *) &s[i]);
        /* 현재 문자가 cc와 같지 않다면
         * 문자열 내에서 다음 문자로 이동합니다.
         */
        i++;
    }
    /* 전체 문자열을 반복한 후, cc의 발생 위치를 찾지 못했다면
     * cc가 '\0'과 같은지 확인해야 합니다.
     * 따라서 현재 문자가 cc와 같은지 다시 한번 확인합니다.
     * 만약 같다면, '\0'의 주소를 char Pointer로 반환합니다.
     */
    if (s[i] == cc)
        return ((char *) &s[i]);
    /* 이 지점에 도달했다면, 문자열 내에서 cc의 어떤 발생 위치도 찾지 못했다는 의미이므로
     * 매뉴얼에 명시된 대로 NULL을 반환합니다.
     */
    return (NULL);
}
```
{% endcode %}

</details>