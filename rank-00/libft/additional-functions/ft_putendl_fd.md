# ft\_putendl\_fd

### 주제

{% code overflow="wrap" %}
```
FT_PUTENDL_FD (단순화된 설명)

NAME
    ft_putendl_fd -- 지정된 파일 기술자(file descriptor)에 문자열을 작성하고, 그 뒤에 개행 문자(newline)를 추가합니다.
SYNOPSIS
    void ft_putendl_fd(char *s, int fd);
DESCRIPTION
    ft_putendl_fd()는 문자열 s와 그 뒤에 개행 문자를 파일 기술자 fd에 작성합니다.
PARAMETERS
    s: 작성할 문자열
    fd: 작성 대상 파일 기술자
RETURN VALUES
    ft_putendl_fd()는 아무것도 반환하지 않습니다.
AUTHORIZED EXTERNAL FUNCTIONS
    write(2)
```
{% endcode %}

### 이해하기 쉬운 설명

이 함수는 매우 간단합니다. 만약 이미 `ft_putstr_fd()` 함수를 구현했다면, 이 함수도 스스로 해결할 수 있을 것이라고 생각합니다. 작동 방식은 동일하며, 단지 문자열 끝에 `newline` 문자를 추가할 뿐입니다.

### 힌트

위에서 언급했듯이, `ft_putstr_fd()`를 어떻게 구현했는지 확인해 보세요. 그 내용을 기반으로 한다면 이 함수는 상당히 쉬울 것입니다.

### 주석이 달린 해답

<details>

<summary>ft_putendl_fd</summary>

{% code title="ft_putendl_fd.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    ft_putendl_fd(char *str, int fd)
{
    int    i;
    
    i = 0;
    while (str[i])
    {
        write(fd, &str[i], 1);
        i++;
    }
    write(fd, "\n", 1);    
}
```
{% endcode %}

</details>