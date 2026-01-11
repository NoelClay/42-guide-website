# tab\_mult

### 과제

```
Assignment name  : tab_mult
Expected files   : tab_mult.c
Allowed functions: write
--------------------------------------------------------------------------------

숫자의 구구단을 출력하는 프로그램을 작성하십시오.

인자는 항상 int 자료형에 맞는 엄격한 양수여야 하며, 해당 숫자에 9를 곱한 값 역시 int 자료형에 맞아야 합니다.

만약 인자가 없다면, 프로그램은 \n을 출력합니다.

예시:

$>./tab_mult 9
1 x 9 = 9
2 x 9 = 18
3 x 9 = 27
4 x 9 = 36
5 x 9 = 45
6 x 9 = 54
7 x 9 = 63
8 x 9 = 72
9 x 9 = 81
$>./tab_mult 19
1 x 19 = 19
2 x 19 = 38
3 x 19 = 57
4 x 19 = 76
5 x 19 = 95
6 x 19 = 114
7 x 19 = 133
8 x 19 = 152
9 x 19 = 171
$>
$>./tab_mult | cat -e
$
$>
```

### 주석 처리된 해답

<details>

<summary>tab_mult</summary>

{% code title="tab_mult.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

// 항상 엄격한 양의 정수를 받을 것이므로 간단한 atoi 함수를 구현합니다
int ft_atoi(char *s)
{
    int res = 0, i = 0;
    while (s[i] && s[i] >= 48 && s[i] <= 57)
    {
        res *= 10;
        res += s[i] - 48;
        i++;
    }
    return (res);
}

// 항상 엄격한 양의 정수를 받을 것이므로 간단한 putnbr 함수를 구현합니다
void ft_putnbr(int nbr)
{
    if (nbr >= 10)
        ft_putnbr(nbr / 10);
    char c = nbr % 10 + '0';
    write(1, &c, 1);
}

void ft_putstr(char *s)
{
    int i = 0;
    while (s[i])
        write(1, &s[i++], 1);
}

int main(int ac, char **av)
{
    if (ac != 2)
    {
        write(1, "\n", 1);
        return (0);
    }
    int i = 1, n = ft_atoi(av[1]);
    // 1부터 9까지 반복합니다
    // 각 줄을 [i x n = i*n] 형식으로 출력합니다
    while (i < 10)
    {
        ft_putnbr(i);
        ft_putstr(" x ");
        ft_putnbr(n);
        ft_putstr(" = ");
        ft_putnbr(i * n);
        write(1, "\n", 1);
        i++
    }
}
```
{% endcode %}

</details>
