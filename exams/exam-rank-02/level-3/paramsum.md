# paramsum

### 주제

```
과제 이름      : paramsum
필요한 파일    : paramsum.c
허용 함수      : write
--------------------------------------------------------------------------------

프로그램에 전달된 인자의 개수를 표시하고, 그 뒤에 개행 문자(newline)를 표시하는 프로그램을 작성해야 합니다.

인자가 없는 경우에는 0을 표시하고 그 뒤에 개행 문자를 표시합니다.

예시:

$>./paramsum 1 2 3 5 7 24
6
$>./paramsum 6 12 24 | cat -e
3$
$>./paramsum | cat -e
0$
$>
```

### 주석 처리된 해답

<details>

<summary>paramsum</summary>

{% code title="paramsum.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

// 간단한 putnbr 함수입니다. 프로그램에 전달되는 인자는 0개 미만일 수 없으므로 
// 음수는 처리할 필요가 없습니다.
void ft_putnbr(int nbr)
{
    if (nbr >= 10)
        ft_putnbr(nbr / 10);
    char c = nbr % 10 + '0';
    write(1, &c, 1);
}

int main(int ac, char **av)
{
    // av(argv) 인자를 사용하지 않을 것이므로 이를 무시하기 위해 (void)로 캐스팅합니다.
    (void) av;
    // argc에서 1을 뺀 값으로 인자의 개수를 출력합니다.
    // 카운트된 첫 번째 인자는 실행 파일 이름이므로, 이 과제의 요구사항에 따른 인자가 아니기 때문입니다.
    ft_putnbr(ac - 1);
    write(1, "\n", 1);
    return (0);
}
```
{% endcode %}

</details>