# ft\_striteri

### 주제

{% code overflow="wrap" %}
```
FT_STRITERI (간소화)

이름
    ft_striteri -- String의 각 문자에 함수를 적용합니다 (인덱스 지정)
개요
    void ft_striteri(char *s, void (*f)(unsigned int, char*));
설명
    String 's'의 각 문자에 함수 'f'를 적용하며, 해당 문자의 인덱스를 첫 번째 인자로 전달합니다.
    각 문자는 'f'로 주소(address)를 통해 전달되므로, 필요한 경우 수정할 수 있습니다.
```
{% endcode %}

### 이해하기 쉬운 설명

`ft_striteri`는 `ft_strmapi`와 동일한 방식으로 작동합니다. `ft_strmapi`에 대한 설명을 먼저 확인하신 다음, 이 문서로 돌아오시기 바랍니다.

`ft_striteri`와 `ft_strmapi`의 차이점은 `ft_striteri`는 아무것도 반환하지 않으며 원본 String에 직접적으로 작업한다는 점입니다.

### 힌트

이 함수는 두 개의 인자를 받습니다. 첫 번째 인자는 String이며, 두 번째 인자는 함수입니다.

`ft_striteri`가 수행하는 역할은 String `s`의 모든 문자에 함수 `f`를 적용하는 것입니다.

이 함수는 String 내 문자의 인덱스와 해당 문자 Pointer를 함수 `f`에 전달합니다.

함수 `f`는 원본 String에 있는 문자의 값을 직접 수정합니다.

최종적으로 우리는 아무것도 반환할 필요가 없지만, 원본 String은 수정된 상태가 됩니다.

### 주석이 달린 해답

<details>

<summary>ft_striteri</summary>

{% code title="ft_striteri.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void ft_striteri(char *s, void (*f)(unsigned int, char*))
{
    unsigned int i;
    
    i = 0;
    /* 원본 String 전체를 반복합니다 */
    while (s[i])
    {
        /* 인덱스 i에 있는 문자에 함수 f를 적용합니다.
         * i와 s[i]의 주소(address)를 f에 인자로 전달합니다.
         * f는 원본 String을 직접 업데이트합니다.
         */
        (*f)(i, &s[i]);
        i++;
    }
}
```
{% endcode %}

</details>