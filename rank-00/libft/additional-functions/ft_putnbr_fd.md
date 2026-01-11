# ft\_putnbr\_fd

### 주제 (Subject)

{% code overflow="wrap" %}
```
FT_PUTNBR_FD (simplified)

NAME
    ft_putnbr_fd -- 지정된 파일 디스크립터에 int를 작성합니다.
SYNOPSIS
    void ft_putnbr_fd(int n, int fd);
DESCRIPTION
    ft_putnbr_fd()는 파일 디스크립터 fd에 int n을 작성합니다.
PARAMETERS
    n: 작성할 int
    fd: 작성할 파일 디스크립터
RETURN VALUES
    ft_putnbr_fd()는 아무것도 반환하지 않습니다.
AUTHORIZED EXTERNAL FUNCTIONS
    write(2)
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

이 함수는 Piscine 과정에서 작성했던 `ft_putnbr()` 함수와 동일하게 작동하며, `ft_putchar_fd()`, `ft_putstr_fd()`, `ft_putendl_fd()`와 마찬가지로 `fd` 매개변수를 받습니다.

이 설명만으로도 함수를 구현할 수 있으리라 생각합니다.

### 힌트 (Hints)

문자 `0`의 ASCII 코드가 숫자 `0`이 아니라는 점을 잊지 마십시오. Piscine 코드 ([`C05 - Ex04`](https://github.com/Laendrun/piscine42/blob/main/c04/ex02/ft_putnbr.c))를 확인해 보시면 도움이 될 것입니다. 작동 방식이 같습니다.

### 주석이 달린 해결책 (Commented solution)

<details>

<summary>ft_putnbr_fd</summary>

{% code title="ft_putnbr_fd.c" overflow="wrap" lineNumbers="true" %}
```clike
#include "libft.h"

void    ft_putnbr_fd(int n, int fd)
{
    /* INT_MIN은 INT_MAX * -1과 같지 않으므로, 이 코드는 INT_MIN을 확인합니다. 
     * 따라서 INT_MIN인 경우 직접 값을 작성할 수 있습니다.
     */
    if (n == -2147483648)
        write(fd, "-2147483648", 11);
    /* 숫자가 0보다 작다면, 먼저 '-' 부호를 작성해야 합니다.
     * 따라서 '-'를 작성한 다음,
     * 숫자를 양수로 만들기 위해 -1을 곱합니다.
     * 그리고 양수가 된 숫자를 가지고 ft_putnbr_fd 함수를 호출합니다.
     */
    else if (n < 0)
    {
        write(fd, "-", 1);
        n = -n;
        ft_putnbr_fd(n, fd);
    }
    else
    {
        /* 여기서 숫자는 양수입니다.
         * 가장 먼저 해야 할 일은 숫자를 각 자릿수로 변환하는 것입니다.
         * 이를 위해 정수 나누기(integer division)를 사용하여 숫자를 10으로 나눈 값으로
         * 함수를 다시 호출합니다. 이렇게 하면 끝에서 한 자릿수가 제거됩니다.
         * 이 작업을 완료하면, 숫자를 10으로 모듈러(%) 연산한 값으로
         * 함수를 호출하여 정수 나눗셈의 나머지를 얻을 수 있으며, 이 방법으로 각 자릿수를 얻습니다.
         * 함수에 전달하는 숫자가 여전히 0보다 크다면,
         * 숫자를 10으로 나눈 값으로 함수를 호출하고, 그 다음 10으로 모듈러 연산한 값으로
         * 함수를 다시 호출하는 동일한 작업을 수행합니다.
         */
        if (n > 9)
        {
            ft_putnbr_fd(n / 10, fd);
            ft_putnbr_fd(n % 10, fd);
        }
        else
        {
            /* 여기에 도달했다면, n은 한 자릿수임을 의미합니다.
             * n이 한 자릿수일 때, 이를 ASCII 테이블의 해당 문자로 변환하여
             * 작성할 수 있습니다.
             * 다른 함수들과 마찬가지로, write 함수의 첫 번째 매개변수는 fd로 설정합니다.
             */
            digit = n + 48;
            write(fd, &digit, 1);
        }
    }
}
```
{% endcode %}

</details>
