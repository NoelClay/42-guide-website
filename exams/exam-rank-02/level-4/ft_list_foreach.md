# ft\_list\_foreach

### 주제

{% code overflow="wrap" %}
```
Assignment name  : ft_list_foreach
Expected files   : ft_list_foreach.c, ft_list.h
Allowed functions:
--------------------------------------------------------------------------------

Write a function that takes a list and a function pointer, and applies this
function to each element of the list.

It must be declared as follows:

void    ft_list_foreach(t_list *begin_list, void (*f)(void *));

The function pointed to by f will be used as follows:

(*f)(list_ptr->data);

You must use the following structure, and turn it in as a file called
ft_list.h:

typedef struct    s_list
{
    struct s_list *next;
    void          *data;
}                 t_list;

```
List와 function Pointer를 인자로 받아, 해당 함수를 List의 각 원소에 적용하는 함수를 작성해야 합니다.

이 함수는 다음과 같이 선언되어야 합니다:

`void    ft_list_foreach(t_list *begin_list, void (*f)(void *));`

`f`가 가리키는 함수는 다음과 같이 사용됩니다:

`(*f)(list_ptr->data);`

다음 구조체를 사용해야 하며, 이 구조체를 `ft_list.h`라는 파일로 제출해야 합니다.

```c
typedef struct    s_list
{
    struct s_list *next;
    void          *data;
}                 t_list;
```

### 주석 처리된 해답

<details>

<summary>ft_list_foreach</summary>

{% code title="ft_list_foreach.c" overflow="wrap" lineNumbers="true" %}
```c
// 다음 구조체를 사용해야 하며, 이 구조체를 ft_list.h라는 파일로 제출해야 합니다:

typedef struct    s_list
{
    struct s_list *next;
    void          *data;
}                 t_list;

#include "stddef.h"

void	ft_list_foreach(t_list *begin_list, void (*f)(void *))
{
	while (begin_list != NULL)
	{
		if (begin_list->data != NULL)
			(*f)(begin_list->data);
		begin_list = begin_list->next;
	}
}
```
{% endcode %}

</details>