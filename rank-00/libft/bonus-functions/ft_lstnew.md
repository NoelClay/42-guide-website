# ft\_lstnew

### 주제

{% code overflow="wrap" %}
```
FT_LSTNEW (simplified)

NAME
    ft_lstnew -- create a new list node element
SYNOPSIS
    t_list *ft_lstnew(void *content);
DESCRIPTION
    Allocate (with malloc(3)) and return the new element. The member variable 'content' is initialized with the value of the 'content' parameter. The 'next' variable is initialized to NULL.
PARAMETERS
    content: The content of the new element
RETURN VALUES
    The new element.
AUTHORIZED EXTERNAL FUNCTIONS
    malloc(3)
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 `t_list` 타입의 새로운 요소를 위한 메모리를 할당하고, 해당 요소의 `content`를 매개변수 `content`로 설정하며, `next` 변수를 `NULL`로 설정합니다.

그런 다음 새로 할당/생성된 리스트 요소를 반환합니다.

### 힌트

{% code title="ft_lstnew" overflow="wrap" lineNumbers="true" %}
```c
/* 새로운 리스트 요소 선언 */
/* 메모리를 할당합니다 */
/* 새로운 요소 변수들을 설정합니다. 
 * new->content = content
 * new->next = NULL
 */
/* 새로운 요소를 반환합니다 */
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_lstnew</summary>

{% code title="ft_lstnew.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

t_list *ft_lstnew(void *content)
{
    /* 새로운 리스트 요소 선언
     */
    t_list *elem;
    
    /* 새로운 요소를 위한 메모리 할당
     */
    elem = malloc(sizeof(t_list));
    if (!elem)
        return (NULL);
    /* 새로운 요소의 content를 'content' 매개변수로 설정하고
     * 'next'를 NULL로 설정합니다
     */
    elem->content = content;
    elem->next = NULL;
    /* 마지막으로, 생성된 요소를 반환합니다
     */
    return (elem);
}
```
{% endcode %}

</details>