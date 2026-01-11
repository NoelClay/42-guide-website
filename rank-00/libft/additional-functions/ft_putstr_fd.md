# ft\_putstr\_fd

### 주제

```
FT_PUTSTR_FD (간소화)

이름
    ft_putstr_fd -- 지정된 file descriptor에 string을 작성합니다
개요
    void ft_putstr_fd(char *s, int fd);
설명
    file descriptor fd에 string s를 작성합니다.
매개변수
    s: 작성할 string
    fd: string을 작성할 file descriptor
반환 값
    ft_putstr_fd()는 아무것도 반환하지 않습니다.
허용된 외부 함수
    write(2)
```

### 이해하기 쉬운 설명

이 함수는 매우 간단합니다. 이미 `ft_putstr()` 함수를 작성하는 방법을 알고 계실 것입니다. 만약 기억이 나지 않으신다면, Piscine 과정에서 했던 것을 다시 한번 살펴보시기 바랍니다.

### 힌트

`write(2)` 함수의 man 페이지를 살펴보십시오. 첫 번째 매개변수는... 예상하셨겠지만, 바로 file descriptor입니다! 그러니 이 코드를 실제로 작성하는 데 정말 어려움이 있으실까요?

스스로 해결하실 수 있으리라 믿습니다.

### 주석 처리된 해답

<details>

<summary>ft_putstr_fd</summary>

{% code title="ft_putstr_fd.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

void    ft_putstr_fd(char *str, int fd)
{
    int i;
    
    i = 0;
    while (str[i])
    {
        write(fd, &str[i], 1);
        i++;
    }
}
```
{% endcode %}

</details>
