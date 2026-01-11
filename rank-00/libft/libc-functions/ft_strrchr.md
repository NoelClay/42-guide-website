# ft\_strrchr

### Subject

{% code overflow="wrap" %}
```
STRRCHR(3) (단순화됨)

NAME
    strrchr -- 문자열 내에서 문자 위치 찾기
SYNOPSIS
    char *strrchr(const char *s, int c);
DESCRIPTION
    strrchr() 함수는 strchr() 함수와 동일하지만, c의 '마지막' 출현 위치를 찾습니다.
RETURN VALUES
    strrchr() 함수는 찾은 문자에 대한 Pointer를 반환하거나, 문자열에 문자가 존재하지 않는 경우 NULL을 반환합니다.
```
{% endcode %}

### Understandable explanation

이 함수는 이해하기 상당히 쉽습니다. `strchr()`과 동일하게 동작하지만, `c`의 '마지막' 출현 위치를 찾는다는 점만 다릅니다.

### Hints

{% code title="ft_strrchr.c" overflow="wrap" lineNumbers="true" %}
```c
char *ft_strrchr(const char *s, int c)
{
    /* 기본적으로 ft_strchr()와 동일한 코드를 사용할 수 있지만, 
     * 문자를 찾았을 때 즉시 값을 반환하지 않고,
     * 매번 변수를 설정한 다음 함수 끝에서 해당 변수를 반환합니다.
     */
    /* 전체 문자열을 반복합니다. */
    /* 현재 문자가 찾아야 하는 문자와 동일한지 확인합니다. */
    /* 전체 문자열을 반복한 후, 찾아야 하는 문자가 '\0'인 경우를 대비하여
     * 해당 문자를 다시 확인합니다.
     */
    /* 문자열 내에서 c를 찾지 못했다면 NULL을 반환합니다. */
}
```
{% endcode %}

### Commented solution

<details>

<summary>ft_strrchr</summary>

{% code title="ft_strrchr.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_strrchr(const char *s, int c)
{
    unsigned int i;
    char *res;
    char cc;
    
    /* c는 int 형태로 전달되었으므로, char로 변환합니다. */
    cc = (char) c;
    /* c의 출현을 전혀 찾지 못하는 경우 함수가 NULL을 반환하도록 
     * 처음에 res를 NULL로 설정합니다.
     */
    res = NULL;
    i = 0;
    /* 전체 문자열 s를 반복합니다. */
    while (s[i])
    {
         /* 현재 문자가 cc와 같다면, 
         * 문자열에서 cc의 출현 위치를 찾았다는 의미입니다.
         * 따라서, 해당 문자의 주소로 res를 설정합니다.
         */
        if (s[i] == cc)
            res = (char *) &s[i];
        /* 그런 다음, cc의 다른 출현 위치를 찾기 위해 문자열에서 다음으로 진행합니다. */
        i++;
    }
    /* 전체 문자열을 반복한 후, cc의 출현을 찾지 못했더라도 
     * cc가 '\0'과 같은지 확인해야 합니다.
     * 따라서 현재 문자가 cc와 같은지 다시 한번 확인합니다.
     * 이 경우, res를 '\0' 문자의 주소로 설정합니다.
     */
    if (s[i] == c)
        res = (char *) &s[i];
    /* 함수 끝에 도달하면 res를 반환합니다.
     * 전체 문자열을 반복하면서 찾은 c의 마지막 출현 위치 주소로 res를 설정했으므로, 
     * 이 값은 c의 마지막 출현 위치를 가리키는 Pointer를 반환하게 됩니다.
     * 만약 c의 출현을 찾지 못했다면, 함수 시작 시 res가 NULL로 설정되었으므로
     * 함수는 NULL을 반환합니다.
     */
    return (res);
}
```
{% endcode %}

</details>