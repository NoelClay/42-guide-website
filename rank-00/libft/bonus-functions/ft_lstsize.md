# ft\_lstsize

### 주제 (Subject)

{% code overflow="wrap" %}
```
FT_LSTSIZE (요약)

NAME
    ft_lstsize -- 리스트의 요소 개수를 반환합니다.
SYNOPSIS
    int *ft_lstsize(t_list *lst);
DESCRIPTION
    리스트의 요소 개수를 셉니다.
PARAMETERS
    lst: 리스트의 시작 부분입니다.
RETURN VALUES
    리스트의 크기입니다.
AUTHORIZED EXTERNAL FUNCTIONS
    None
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

이 함수가 무엇을 하는지에 대해서는 주제가 명확하다고 생각합니다. 저희는 리스트의 요소 개수를 반환해야 합니다.

### 힌트 (Hints)

{% code overflow="wrap" %}
```c
/* 리스트를 순회합니다 */
/* 카운트를 반환합니다 */
```
{% endcode %}

### 주석이 달린 해답 (Commented solution)

<details>

<summary>ft_lstsize</summary>

{% code title="ft_lstsize.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int ft_lstsize(t_list *lst)
{
    /* 기존 리스트를 수정하지 않도록 임시(tmp) 변수를 사용했습니다. */
    t_list *tmp;
    int i;
    
    tmp = lst;
    i = 0;
    /* 마지막 요소의 next가 NULL을 가리키므로, tmp가 NULL이 아닐 때까지 반복합니다.
     * 이렇게 하면 리스트의 모든 요소를 순회하게 됩니다.
     */
    while (tmp)
    {
        /* tmp를 다음(next) 요소로 설정합니다. */
        tmp = tmp->next;
        i++;
    }
    /* 카운트를 반환합니다. */
    return (i);
}
```
{% endcode %}

</details>
```