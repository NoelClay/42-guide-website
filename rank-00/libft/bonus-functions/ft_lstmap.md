# ft\_lstmap

### 주제

{% code overflow="wrap" %}
```
FT_LSTMAP (단순화)

이름
    ft_lstmap -- 각 요소에 f를 적용하여 생성된 새 리스트를 만듭니다
개요
    t_list *ft_lstmap(t_list *lst, void (*f)(void *), void (*del)(void *));
설명
    리스트 'lst'를 순회하며 각 요소의 내용(content)에 함수 'f'를 적용합니다. 'f'를 연속적으로 적용한 결과로 새로운 리스트를 생성합니다. 함수 'del'은 필요한 경우 요소의 내용(content)을 파괴(destroy)하는 데 사용됩니다.
매개변수
    lst: 한 요소의 Pointer 주소
    f: 적용할 함수의 주소
    del: 요소의 내용(content)을 삭제할 수 있는 함수의 주소
반환 값
    없음
허용된 외부 함수
    없음
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 `ft_lstiter` 함수와 유사하게 작동하지만, 각 요소의 내용(content)에 'f'를 연속적으로 적용한 결과로 새로운 리스트를 생성합니다.

### 힌트

```c
/* lst나 f나 del이 NULL인지 확인합니다 */
/* lst를 순회합니다 */
    /* 새 요소를 생성합니다 */
    /* 새 요소가 null이면 새 리스트를 비웁니다 (clear) */
/* 새 요소를 리스트의 맨 뒤에 추가합니다 */
/* 마지막으로, 새 리스트를 반환합니다 */
```

### 주석이 달린 해결책

<details>

<summary>ft_lstmap</summary>

```c
#include "libft.h"

t_list *ft_lstmap(t_list *lst, void *(*f)(void *), void (*del)(void *))
{
    t_list *new_list;
    t_list *new_obj;
    
    if (!lst || !f || !del)
        return (NULL);
    new_list = NULL;
    /* 기존 리스트를 순회합니다 */
    while (lst)
    {
        /* 현재 요소의 내용(content)에 함수 f를 적용한 결과를
         * 내용(content)으로 하는 새 객체를 생성합니다
         */
        new_obj = ft_lstnew(f(lst->content));
        if (!new_obj)
        {
            /* 새 객체가 null이면 새 리스트를 비웁니다 (clear) */
            ft_lstclear(&new_list, del);
            return (NULL);
        }
        /* 새 객체가 있다면, 새 리스트의 맨 뒤에 추가합니다 */
        ft_lstadd_back(&new_list, new_obj);
        lst = lst->next;
    }
    /* 마지막으로, 새 리스트를 반환합니다 */
    return (new_list);
}
```

</details>
