# ft\_lstlast

### 과제

{% code overflow="wrap" %}
```
FT_LSTLAST (간소화)

이름
    ft_lstlast -- 리스트의 마지막 요소를 가져옵니다
개요
    t_list *ft_lstlast(t_list *lst);
설명
    리스트의 마지막 요소를 반환합니다
매개변수
    lst: 리스트의 시작 부분입니다
반환 값
    리스트의 마지막 요소
허용된 외부 함수
    없음
```
{% endcode %}

### 이해하기 쉬운 설명

이번 과제는 주제가 충분히 명확하다고 생각합니다. 우리는 리스트의 마지막 요소에 대한 Pointer를 반환해야 하며, 이해하기 매우 쉽습니다.

### 힌트

이 함수는 기본적으로 `ft_lstsize` 함수와 동일하게 작동하지만, 리스트에 몇 개의 요소가 있는지 세거나 그 개수를 반환할 필요는 없습니다. 대신, `tmp` 요소를 직접 반환하면 됩니다.

### 주석 처리된 해답

<details>

<summary>ft_lstlast</summary>

{% code title="ft_lstlast.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

t_list *ft_lstlast(t_list *lst)
{
    t_list *tmp;
    
    if (!lst)
        return (NULL);
    tmp = lst;
    /* 요소를 직접 순회하는 대신, 다음 요소가 있는지 확인합니다.
     * 다음 요소가 없다면, 리스트의 끝에 도달했다는 의미이므로
     * 현재 Pointer를 반환해야 합니다.
     * 만약 ft_lstsize처럼 요소를 직접 순회했다면
     * 항상 NULL을 반환하게 될 것입니다.
     */
    while (tmp->next)
        tmp = tmp->next;
    return (tmp);
}
```
{% endcode %}

</details>