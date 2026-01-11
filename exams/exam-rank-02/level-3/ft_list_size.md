# ft\_list\_size

## 주제

```
과제 이름       : ft_list_size
제출 파일      : ft_list_size.c, ft_list.h
허용 함수      :
--------------------------------------------------------------------------------

인자로 전달된 연결 리스트(linked list)의 요소(element) 개수를 반환하는 함수를 작성하십시오.

이 함수는 다음과 같이 선언되어야 합니다:

int	ft_list_size(t_list *begin_list);

다음 구조체를 사용해야 하며, 이를 ft_list.h라는 파일로 제출해야 합니다:

typedef struct    s_list
{
    struct s_list *next;
    void          *data;
}                 t_list;
```

## 주석 처리된 해답

{% hint style="warning" %}
`ft_list.h` 파일도 함께 제출하는 것을 잊지 마십시오.
{% endhint %}

<details>

<summary>ft_list_size()</summary>

{% code title="ft_list_size.c" overflow="wrap" lineNumbers="true" %}
```c
#include "ft_list.h"

int ft_list_size(t_list *begin_list)
{
    int i = 0;
    
    // 다음 요소가 null이 아닐 때까지 리스트 요소들을 순회합니다.
    while (begin_list->next)
    {
        // 원래의 Pointer를 다음 요소를 가리키는 Pointer와 같게 설정하고
        // 카운터를 증가시킵니다.
        begin_list = begin_list->next;
        i++;
    }
    // 카운터를 반환합니다.
    return (i);
}
```
{% endcode %}

</details>