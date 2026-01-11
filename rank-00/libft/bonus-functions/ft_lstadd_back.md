# ft\_lstadd\_back

### 주제

{% code overflow="wrap" %}
```
FT_LSTADD_BACK (간소화)

NAME
    ft_lstadd_back -- 리스트의 끝에 새로운 노드를 추가합니다
SYNOPSIS
    void ft_lstadd_back(t_list **lst, t_list *new);
DESCRIPTION
    리스트의 끝에 'new' 요소를 추가합니다
PARAMETERS
    lst: 리스트의 첫 번째 요소의 Pointer 주소입니다
    new: 리스트에 추가할 새 요소의 Pointer 주소입니다
RETURN VALUES
    없음
AUTHORIZED EXTERNAL FUNCTIONS
    없음
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 기존 리스트의 끝에 새로운 요소를 추가할 수 있도록 해줍니다.

### 힌트

```c
/* 리스트의 마지막 요소를 가져옵니다 */
/* last->next 변수가 새 요소를 가리키도록 설정합니다 */
/* last가 NULL인 경우, 리스트 Pointer가 새 요소를 가리키도록 설정합니다 */
```

### 주석이 달린 해결책

<details>

<summary>ft_lstadd_back</summary>

{% code title="ft_lstadd_back.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void ft_lstadd_back(t_list **alst, t_list *new)
{
    t_list *last;
    
    /* ft_lstlast를 사용하여 리스트의 마지막 요소를 가져옵니다
     */
    last = ft_lstlast(*alst);
    /* last가 NULL인 경우 리스트가 없다는 의미이므로, 리스트 Pointer가
     * 새 요소를 가리키도록 설정합니다
     */
    if (!last)
        *alst = new;
    /* last의 next 변수가 새 요소를 가리키도록 설정합니다
     */
    last->next = new;
}
```
{% endcode %}

</details>