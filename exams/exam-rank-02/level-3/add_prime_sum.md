# add\_prime\_sum

### 주제

```
Assignment name  : add_prime_sum
Expected files   : add_prime_sum.c
Allowed functions: write, exit
--------------------------------------------------------------------------------

Write a program that takes a positive integer as argument and displays the sum
of all prime numbers inferior or equal to it followed by a newline.

If the number of arguments is not 1, or the argument is not a positive number,
just display 0 followed by a newline.

Yes, the examples are right.

Examples:

$>./add_prime_sum 5
10
$>./add_prime_sum 7 | cat -e
17$
$>./add_prime_sum | cat -e
0$
$>
```

과제 이름: add_prime\_sum
필수 파일: add\_prime\_sum.c
허용 함수: write, exit
--------------------------------------------------------------------------------

양의 정수를 인자로 받아, 그 숫자보다 작거나 같은 모든 소수(prime number)들의 합을 출력하고 개행(newline)하는 프로그램을 작성하십시오.

만약 인자의 개수가 1이 아니거나, 인자가 양의 정수가 아니라면, 단순히 0을 출력하고 개행합니다.

예제는 정확합니다.

예제:

$>./add\_prime\_sum 5
10
$>./add\_prime\_sum 7 | cat -e
17$
$>./add\_prime\_sum | cat -e
0$
$>

### 주석이 달린 해답

사실 몇몇 숫자 사이에서 단순한 덧셈을 수행하는 작업 자체는 그리 복잡하지 않습니다. 진짜 도전 과제는 보조 함수를 구현하는 것입니다. `atoi` 함수를 어떻게 구현하는지 기억하시기를 바랍니다.

<details>

<summary>add_prime_sum.c</summary>

{% code title="add_prime_sum.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

void ft_putchar(char c);
void ft_putnbr(int nbr);
int is_prime(int nbr);
int ft_atoi(char *str);

void ft_putchar(char c)
{
    write(1, &c, 1);
}

void ft_putnbr(int nbr)
{
    unsigned int nb;
    
    if (nbr < 0)
    {
        ft_putchar('-');
        nb = -nbr;
    }
    else
    	nb = nbr;
    if (nb >= 10)
        ft_putnbr(nb / 10);
    ft_putchar(nb % 10 + '0');
}

// 숫자가 소수인지 확인합니다.
int is_prime(int nbr)
{
    // nbr이 2보다 작다면 (소수 여부를 확인하기 위해, 2는 첫 소수입니다)
    if (nbr < 2)
        return (0);
    // 소수가 아니라는 의미로 0을 반환할 수 있습니다.
    
    // nbr의 절반까지 모든 약수를 확인합니다.
    // nbr/2를 초과하여 확인하는 것은 불필요합니다. 예를 들어 보겠습니다.
    // nbr = 10; nbr/2 = 5입니다. 만약 3을 확인했다면, 6을 확인하는 것은
    // 동일한 결과를 줄 것입니다.
    int i = 2;
    while (i <= nbr / 2)
    {
        if (nbr % i == 0)
            return (0);
        i++;
    }
    return (1);
}

// 이것이 어떻게 작동하는지 알고 계실 겁니다. (ft_atoi 함수입니다.)
int ft_atoi(char *str)
{
    int res = 0, sign = 1, i = 0;
    while (str[i] == ' ')
        i++;
    if (str[i] == '+' || str[i] == '-')
        if (str[i++] == '-')
            sign = -1;
    while (str[i] && str[i] >= 48 && str[i] <= 57)
    {
        res *= 10;
        res += str[i] - 48;
        i++;
    }
    res *= sign;
    return (res);
}

int main(int ac, char **av)
{
    if (ac != 2 || ft_atoi(av[1]) <= 0)
    {
        write(1, "0\n", 2);
        return (0);
    }
    int n = ft_atoi(av[1]);
    
    // 다음은 add_prime_sum의 실제 로직입니다.
    // 커맨드 라인을 통해 주어진 숫자까지 모든 숫자를 확인하고,
    // 해당 숫자가 소수(prime)라면, 전체 합계에 더합니다.
    int sum = 0;
    while (n > 1)
    {
        if (is_prime(n))
            sum += n;
        n--;
    }
    ft_putnbr(sum);
    ft_putchar('\n');
}
```
{% endcode %}

</details>