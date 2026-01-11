# ft\_strmapi

### 주제

{% code overflow="wrap" %}
```
FT_STRMAPI() (간소화)

NAME
    ft_strmapi -- 문자열의 각 문자에 함수를 적용합니다
SYNOPSIS
    char *ft_strmapi(const char *s, char (*f)(unsigned int, char));
DESCRIPTION
    함수 'f'를 문자열 's'의 각 문자에 적용하여 'f'를 연속적으로 적용한 결과로 새로운 문자열을 생성합니다 (malloc(3)을 사용합니다).
PARAMETERS
    s: 반복할 문자열
    f: 각 문자에 적용할 함수
RETURN VALUES
    ft_strmapi()는 'f'를 연속적으로 적용한 결과인 새로운 문자열을 반환합니다. 메모리 할당에 실패하면 NULL을 반환합니다.
AUTHORIZED EXTERNAL FUNCTIONS
    malloc(3)
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 두 개의 매개변수를 받습니다. 첫 번째는 문자열이고, 두 번째는 함수입니다.

`ft_strmapi`가 하는 일은 문자열 `s`의 모든 문자에 함수 `f`를 적용하는 것입니다.

문자열 내 문자의 인덱스와 해당 문자가 함수 `f`에 전달됩니다.

함수 `f`의 결과는 새로운 문자열의 인덱스 `i`에 위치하게 됩니다.

마지막으로, 문자열의 모든 문자에 `f`를 적용하여 생성된 새로운 문자열을 반환합니다.

### 힌트

먼저, 전체 문자열 길이에 NUL-terminating character를 위한 공간 하나를 더하여 충분한 메모리를 할당해야 합니다.

그런 다음, 문자열 `s`를 순회하면서 `s`의 각 문자에 함수 `f`를 호출할 수 있습니다.

### 주석 처리된 해답

<details>

<summary>ft_strmapi</summary>

{% code title="ft_strmapi.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

char *ft_strmapi(const char *s, char (*f)(unsigned int, char))
{
    unsigned int i;
    char *res;
    
    /* 새로운 문자열을 위해 메모리를 할당합니다 */
    res = malloc((ft_strlen(s) + 1) * sizeof(char));
    if (!res)
        return (NULL);
    i = 0;
    /* 전체 문자열 s를 순회합니다 */
    while (i < ft_strlen(s))
    {
        /* s의 각 문자에 함수 f를 적용하고
         * 그 결과를 새로운 문자열 res에 저장합니다
         */
        res[i] = (*f)(i, s[i]);
        i++;
    }
    /* NUL-terminating character를 설정합니다 */
    res[i] = 0;
    /* 마지막으로, res를 반환합니다 */
    return (res);
}
```
{% endcode %}

</details>