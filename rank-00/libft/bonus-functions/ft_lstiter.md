# ft\_lstiter

### 주제

{% code overflow="wrap" %}
```
FT_LSTITER (간소화)

NAME
    ft_lstiter -- 각 요소의 content에 함수를 적용합니다.
SYNOPSIS
    void ft_lstiter(t_list *lst, void (*f)(void *));
DESCRIPTION
    리스트 'lst'를 순회하며 모든 요소의 content에 함수 'f'를 적용합니다.
PARAMETERS
    lst: 하나의 요소에 대한 Pointer 주소
    f: 적용할 함수
RETURN VALUES
    없음
AUTHORIZED EXTERNAL FUNCTIONS
    없음
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 전체 리스트를 순회하며 각 요소의 content에 함수 `f`를 적용합니다.

### 힌트

```c
/* 전체 리스트를 순회합니다 */
/* 각 요소의 content에 함수 'f'를 적용합니다 */
```

### 주석 처리된 해답

<details>

<summary>ft_lstiter</summary>

{% code title="ft_lstiter.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void ft_lstiter(t_list *lst, void (*f)(void *)
{
    t_list *tmp;
    
    tmp = lst;
    /* tmp가 NULL이 아닐 때까지 반복합니다 */
    while (tmp)
    {
        /* 현재 요소의 content에 함수 f를 적용합니다
         */
        f(tmp->content);
        /* tmp가 다음 요소를 가리키도록 설정합니다 */ 
        tmp = tmp->next;
    }
}
```
{% endcode %}

</details>