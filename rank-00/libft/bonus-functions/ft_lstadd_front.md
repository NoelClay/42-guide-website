# ft\_lstadd\_front

### 주제 (Subject)

{% code overflow="wrap" %}
```
FT_LSTADD_FRONT (simplified)

NAME
    ft_lstadd_front -- Adds a new element at the front of the list
SYNOPSIS
    void ft_lstadd_front(t_list **lst, t_list *new);
DESCRIPTION
    Add the 'new' element at the front of the list
PARAMETERS
    lst: pointer address to the first element of the list
    new: pointer address of the new element to add to the list
RETURN VALUES
    None
AUTHORIZED EXTERNAL FUNCTIONS
    None
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

이 함수는 기존 리스트의 맨 앞에 새 요소를 추가할 수 있도록 해줍니다.

우리는 새 요소와 기존 리스트를 인자로 받습니다.

### 힌트 (Hints)

<pre class="language-c" data-title="ft_lstadd_front" data-overflow="wrap" data-line-numbers><code class="lang-c"><strong>/* 새 요소의 다음 주소가 기존 리스트의 시작 부분을 가리키도록 설정합니다.
</strong><strong> */
</strong><strong>/* 기존 리스트의 Pointer가 새 요소를 가리키도록 설정합니다.
</strong><strong> */
</strong></code></pre>

### 주석이 달린 해답 (Commented solution)

<details>

<summary>ft_lstadd_front</summary>

{% code title="ft_lstadd_front.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void ft_lstadd_front(t_list **alst, t_list *new)
{
    /* 새 요소의 다음 주소가
     * 기존 리스트의 시작 부분을 가리키도록 설정합니다.
     */
    new->next = *alst;
    /* 기존 리스트의 Pointer가 새 요소를 가리키도록 설정합니다.
     */
    *alst = new;
}
```
{% endcode %}

</details>