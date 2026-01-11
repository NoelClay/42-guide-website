# ft\_lstdelone

### 주제

{% code overflow="wrap" %}
```
FT_LSTDELONE (요약)

이름
    ft_lstdelone -- 리스트에서 하나의 요소를 제거합니다
개요
    void ft_lstdelone(t_list *lst, void (*del)(void *));
설명
    매개변수로 전달된 요소의 메모리를 'del' 함수를 사용하여 해제한 다음, free(3)를 사용해 요소를 해제합니다. 'next' 요소의 메모리는 해제해서는 안 됩니다.
매개변수
    lst: 해제할 요소
    del: 요소의 content를 삭제할 수 있는 함수의 주소
반환 값
    없음 (None)
허용된 외부 함수
    free(3)
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 리스트 요소를 매개변수로 받아, 함께 전달된 `del` 함수를 사용하여 요소의 content를 삭제하고 할당된 메모리도 해제합니다.

### 힌트

{% code overflow="wrap" %}
```c
/* 요소의 content에 삭제 함수를 사용합니다 */
/* 요소를 해제합니다 (free) */
```
{% endcode %}

### 주석이 달린 해결책

<details>

<summary>ft_lstdelone</summary>

{% code title="ft_lstdelone.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void ft_lstdelone(t_list *lst, void (*del)(void *))
{
    /* 요소의 content에 del 함수를 사용합니다 */
    del(lst->content);
    /* 요소를 해제합니다 */
    free(lst);
}
```
{% endcode %}

</details>