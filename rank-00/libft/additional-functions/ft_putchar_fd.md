# ft\_putchar\_fd

### 주제

{% code overflow="wrap" %}
```
FT_PUTCHAR_FD (간소화됨)

NAME
    ft_putchar_fd -- 지정된 file descriptor에 문자 c를 작성합니다.
SYNOPSIS
    void ft_putchar_fd(char c, int fd);
DESCRIPTION
    ft_putchar_fd() 함수는 file descriptor fd에 문자 c를 작성합니다.
PARAMETERS
    c: 작성할 문자
    fd: 문자를 작성할 file descriptor
RETURN VALUES
    ft_putchar_fd()는 아무것도 반환하지 않습니다.
AUTHORIZED EXTERNAL FUNCTIONS
    write(2)
```
{% endcode %}

### 이해하기 쉬운 설명

이 기능은 매우 간단합니다. 이미 `ft_putchar()` 함수를 어떻게 작성하는지 알고 계실 것입니다. 만약 기억나지 않으신다면, 피신(Piscine) 기간 동안 수행했던 내용을 다시 확인해 보십시오.

### 힌트

`write(2)` 함수의 man 페이지를 확인해 보십시오. 첫 번째 매개변수는... 예상하셨듯이, file descriptor입니다! 따라서 이 코드 전체가 정말로 필요한지 생각해 보십시오. 스스로 해결할 수 있기를 바랍니다.

### 주석이 달린 해결책

<details>

<summary>ft_putchar_fd</summary>

{% code title="ft_putchar_fd.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    ft_putchar_fd(char c, int fd)
{
    /* 첫 번째 매개변수는 file descriptor입니다.
     * 두 번째 매개변수는 문자의 주소입니다.
     */
    write(fd, &c, 1);
}
```
{% endcode %}

</details>
