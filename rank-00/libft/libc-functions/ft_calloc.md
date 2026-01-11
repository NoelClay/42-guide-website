# ft\_calloc

### 주제

{% code overflow="wrap" %}
```
CALLOC(3) (simplified)

NAME
    calloc -- memory allocation
SYNOPSIS
    void *calloc(size_t count, size_t size);
DESCRIPTION
    The calloc() function allocates memory.
    The allocated memory is aligned such that it can be used for any data type.
    The calloc() function contigously allocates enough space for count objects that are size bytes of memory each and returns a pointer to the allocated memory.
    The allocated memory is filled with bytes of value zero.
RETURN VALUES
    If successful, calloc() returns a pointer to allocated memory. If there is an error, they return a NULL pointer and set errno to ENOMEM.
```
{% endcode %}

### 이해하기 쉬운 설명

지금까지 여러분은 `malloc()` 함수가 무엇을 하는지 이해했을 것입니다. 적어도 저는 그렇게 바랍니다. 그렇지 않다면, `malloc()`의 작동 방식을 이해하고 이 섹션으로 돌아오십시오.
저는 주로 `calloc()`을 `malloc()`과 비교하여 설명하겠습니다.

`calloc()`은 `malloc()`과 동일하게 작동하지만, 차이점은 할당하기 전에 메모리에 남아 있던 알 수 없는(gibberish) 값 대신에 `calloc()`은 모든 메모리 바이트를 `0`으로 설정한다는 것입니다.

### 힌트

{% code title="ft_calloc.c" overflow="wrap" lineNumbers="true" %}
```c
void *ft_calloc(size_t count, size_t size)
{
    /* 임시 unsigned char Pointer를 선언합니다 */
    /* tmp에 count * size를 할당하기 위해 malloc을 사용합니다 */
    /* tmp를 순회하며 각 바이트를 0으로 설정합니다 */
    /* tmp를 반환합니다 */
}
```
{% endcode %}

### 주석이 달린 해결책

<details>

<summary>ft_calloc</summary>

{% code title="ft_calloc.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void *ft_calloc(size_t count, size_t size)
{
    unsigned char *tmp;
    size_t i;
    
    i = 0;
    /* malloc을 사용하여 메모리에 count * size 바이트를 할당합니다 */
    tmp = malloc(count * size);
    /* 메모리가 할당되었는지 확인합니다 */
    if (!tmp)
        return (NULL);
    /* 할당된 모든 바이트를 순회하며 0으로 설정합니다 */
    while (i < count * size)
        tmp[i++] = 0;
    /* 할당된 메모리를 반환합니다 */
    return (tmp);
}
```
{% endcode %}

</details>