# ft\_list\_remove\_if

### 과제

```
과제 이름      : ft_list_remove_if
요구 파일      : ft_list_remove_if.c
허용 함수      : free
--------------------------------------------------------------------------------
```

전달된 리스트에서 참조 데이터와 "동일한" 데이터를 가진 모든 요소를 제거하는 `ft_list_remove_if`라는 함수를 작성하십시오.

이 함수는 다음과 같이 선언됩니다:

`void ft_list_remove_if(t_list **begin_list, void *data_ref, int (*cmp)());`

`cmp` 함수는 두 개의 `void*`를 인자로 받으며, 두 매개변수가 동일할 때 0을 반환합니다.

다음과 같은 내용을 포함하는 `ft_list.h` 파일을 사용해야 합니다:

```
$>cat ft_list.h
typedef struct      s_list
{
    struct s_list   *next;
    void            *data;
}                   t_list;
$>
```
### 주석 처리된 솔루션

<details>

<summary>ft_list_remove_if</summary>

{% code title="ft_list_remove_if.c" overflow="wrap" lineNumbers="true" %}
```c
// 다음 내용을 포함하는 ft_list.h 파일을 사용해야 합니다: ft_list.h

typedef struct      s_list
{
    struct s_list   *next;
    void            *data;
}                   t_list;

#include "ft_list.h"
#include "stdlib.h"

void	ft_list_remove_if(t_list **begin_list, void *data_ref, int (*cmp)())
{
	t_list *tmp;

	if ((*begin_list == NULL) || (begin_list == NULL))
		return ;
	if (cmp((*begin_list)->data, data_ref) == 0x0)
	{
		tmp = *begin_list;
		*begin_list = (*begin_list)->next;
		free(tmp);
		ft_list_remove_if(begin_list, data_ref, cmp);
	}
	else
		ft_list_remove_if(&((*begin_list)->next), data_ref, cmp);
}
```
{% endcode %}

</details>