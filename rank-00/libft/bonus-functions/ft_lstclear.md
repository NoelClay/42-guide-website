# ft\_lstclear

### 주제

{% code overflow="wrap" %}
```
FT_LSTCLEAR (단순화)

NAME
    ft_lstclear -- 파라미터로 전달된 요소와 그 뒤따르는 모든 요소를 제거합니다
SYNOPSIS
    void ft_lstclear(t_list **lst, void (*del)(void *));
DESCRIPTION
    파라미터로 전달된 요소와 그 뒤따르는 모든 요소의 메모리를 'del' 함수와 free(3)을 사용하여 삭제하고 해제합니다. 마지막으로, 초기 Pointer는 NULL로 설정되어야 합니다.
PARAMETERS
    lst: 한 요소에 대한 Pointer 주소입니다
    del: 요소의 content(내용)를 삭제할 수 있는 함수의 주소입니다
RETURN VALUES
    없음
AUTHORIZED EXTERNAL FUNCTIONS
    free(3)
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 `ft_lstdelone` 함수와 유사하게 작동하지만, 단 하나의 요소만 제거하는 대신, 파라미터로 전달된 요소와 그 뒤따르는 모든 요소를 제거합니다.

### 주석 처리된 해답

<details>

<summary>ft_lstclear</summary>

{% code title="ft_lstclear.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void ft_lstclear(t_list *lst, void (*del)(void *))
{
    t_list *tmp;
    /* 리스트를 순회합니다 */
    while (*lst)
    {
        /* tmp가 리스트의 다음 요소를 가리키도록 설정합니다 */
        tmp = (*lst)->next;
        /* 현재 요소에 ft_lstdelone을 사용합니다 */
        ft_lstdelone(*lst, del);
        /* list Pointer를 tmp와 같게 설정하여 다음 요소에 대한 Pointer를 가지도록 합니다 */
        *lst = tmp;
    }
    /* list Pointer를 free하고 NULL로 설정합니다 */
    free(*lst);
    *lst = NULL;
}
```
{% endcode %}

</details>