# print\_hex

### 과제

```
Assignment name  : print_hex
Expected files   : print_hex.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a program that takes a positive (or zero) number expressed in base 10,
and displays it in base 16 (lowercase letters) followed by a newline.

If the number of parameters is not 1, the program displays a newline.

Examples:

$> ./print_hex "10" | cat -e
a$
$> ./print_hex "255" | cat -e
ff$
$> ./print_hex "5156454" | cat -e
4eae66$
$> ./print_hex | cat -e
$
```

10진수로 표현된 양수(또는 0)를 인자로 받아, 이를 16진수(소문자)로 표시하고 뒤에 개행 문자(newline)를 출력하는 프로그램을 작성하십시오.

프로그램 인자의 개수가 1이 아닌 경우, 개행 문자만 출력합니다.

예시를 참고하십시오:

### 주석이 달린 솔루션

<details>

<summary>print_hex.c</summary>

{% code title="print_hex.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

// 파라미터를 unsigned int로 변환하는 간단한 atoi 함수입니다.
// 음수와 기타 예외 상황을 처리해야 할 수도 있지만,
// 과제 지침에는 이러한 입력이 프로그램에 전달될 수 있는지 명시되어 있지 않습니다.
unsigned int ft_atoi(char *str)
{
    unsigned int res = 0, i = 0;

    while (str[i] && str[i] >= '0' && str[i] <= '9')
    {
        res *= 10;
	res += str[i++] - '0';
    }
    return (res);
}

void put_hex(int nbr)
{
    char *digits = "0123456789abcdef";

    if (nbr >= 16)
        put_hex(nbr / 16);
    // 우리가 원하는 숫자에 해당하는 문자를 선택하고
    // 화면에 출력합니다.
    nbr = digits[nbr % 16];
    write(1, &nbr, 1);
}

int main(int ac, char **av)
{
    // 인자가 존재한다면, ft_atoi를 통해 변환된 값을
    // put_hex 함수로 바로 전달합니다.
    if (ac == 2)
        put_hex(ft_atoi(av[1]));
    write(1, "\n", 1);
    return (0);
}
```
{% endcode %}

</details>