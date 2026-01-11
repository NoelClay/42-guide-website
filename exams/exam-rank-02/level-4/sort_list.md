# sort\_list

### 주제

{% code overflow="wrap" %}
```
Assignment name  : sort_list
Expected files   : sort_list.c
Allowed functions:
--------------------------------------------------------------------------------

Write the following functions:

t_list	*sort_list(t_list* lst, int (*cmp)(int, int));

This function must sort the list given as a parameter, using the function
pointer cmp to select the order to apply, and returns a pointer to the
first element of the sorted list.

Duplications must remain.

Inputs will always be consistent.

You must use the type t_list described in the file list.h
that is provided to you. You must include that file
(#include "list.h"), but you must not turn it in. We will use our own
to compile your assignment.

Functions passed as cmp will always return a value different from
0 if a and b are in the right order, 0 otherwise.

For example, the following function used as cmp will sort the list
in ascending order:

int ascending(int a, int b)
{
	return (a <= b);
}

```
{% endcode %}

**번역:**

과제 이름: sort\_list
예상 파일: sort\_list.c
허용된 함수:
--------------------------------------------------------------------------------

다음 함수를 작성해야 합니다:

t_list	*sort_list(t_list* lst, int (*cmp)(int, int));

이 함수는 매개변수로 주어진 리스트를 정렬해야 하며, 함수 Pointer인 `cmp`를 사용하여 적용할 순서를 선택하고, 정렬된 리스트의 첫 번째 요소에 대한 Pointer를 반환합니다.

중복된 요소는 그대로 유지되어야 합니다.

입력은 항상 일관성이 있습니다.

제공된 `list.h` 파일에 설명된 `t_list` 타입을 사용해야 합니다. 해당 파일을 include(`#include "list.h"`) 해야 하지만, 제출할 필요는 없습니다. 저희는 과제 컴파일을 위해 자체 파일을 사용할 것입니다.

`cmp`로 전달되는 함수는 `a`와 `b`가 올바른 순서에 있을 경우 0이 아닌 값을 반환하고, 그렇지 않으면 0을 반환합니다.

예를 들어, `cmp`로 사용되는 다음 함수는 리스트를 오름차순으로 정렬합니다:

```c
int ascending(int a, int b)
{
	return (a <= b);
}
```

---

### 주석 처리된 해답

<details>

<summary>sort_list</summary>

{% code title="sort_list.c" overflow="wrap" lineNumbers="true" %}
```c
#include "list.h"
#include "unistd.h"

t_list	*sort_list(t_list *lst, int (*cmp)(int, int))
{
	int tmp;
	t_list *head;

	head = lst; // 리스트의 시작 주소를 head에 저장합니다.
	while (lst != NULL && lst->next != NULL)
	{
		// cmp 함수가 0을 반환하면 (순서가 잘못되었다면)
		if ((*cmp)(lst->data, lst->next->data) == 0)
		{
			// 데이터 값을 스왑(교환)합니다.
			tmp = lst->data;
			lst->data = lst->next->data;
			lst->next->data = tmp;
			// 정렬을 다시 시작하기 위해 lst를 head로 되돌립니다.
			lst = head;
		}
		else
			// 순서가 맞다면 다음 노드로 이동합니다.
			lst = lst->next;
	}
	return (head); // 정렬된 리스트의 시작(head)을 반환합니다.
}
```
{% endcode %}

</details>