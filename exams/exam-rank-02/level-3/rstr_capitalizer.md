# rstr\_capitalizer

### 주제

```
Assignment name  : rstr_capitalizer
Expected files   : rstr_capitalizer.c
Allowed functions: write
--------------------------------------------------------------------------------

하나 이상의 문자열을 인자로 받는 프로그램을 작성합니다. 각 인자에 대해, 각 단어에서 글자인 마지막 문자를 대문자로, 나머지는 소문자로 바꾼 다음, 그 결과를 출력하고 이어서 \n을 표시합니다.

단어는 공백/탭 또는 문자열의 시작/끝으로 구분된 문자열 섹션입니다. 단어가 한 글자로만 이루어져 있다면, 그 글자는 대문자여야 합니다.

글자(letter)는 [a-zA-Z] 집합에 속하는 문자입니다.

매개변수가 없다면, \n을 출력합니다.

예시:

$> ./rstr_capitalizer | cat -e
$
$> ./rstr_capitalizer "a FiRSt LiTTlE TESt" | cat -e
A firsT littlE tesT$
$> ./rstr_capitalizer "SecONd teST A LITtle BiT   Moar comPLEX" "   But... This iS not THAT COMPLEX" "     Okay, this is the last 1239809147801 but not    the least    t" | cat -e
seconD tesT A littlE biT   moaR compleX$
   but... thiS iS noT thaT compleX$
     okay, thiS iS thE lasT 1239809147801 buT noT    thE leasT    T$
$>
```

### 주석이 달린 해답

<details>

<summary>rstr_capitalizer</summary>

{% code title="rstr_capitalizer.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int ft_isspace(char c)
{
    if (c <= 32)
        return (1);
    return (0);
}

void ft_putchar(char c)
{
    write(1, &c, 1);
}

void r_capitalizer(char *s)
{
    int i = 0;
    while (s[i])
    {
         // 문자가 대문자라면, 소문자로 변경합니다.
         if (s[i] >= 'A' && s[i] <= 'Z')
             s[i] += 32;
         // 이제 소문자인지 확인하고, 다음 문자가
         // 공백 문자라면, 다시 대문자로 변경합니다.
         if ((s[i] >= 'a' && s[i] <= 'z') && ft_isspace(s[i + 1]))
             s[i] -= 32;
         // 이제 모든 문자를 출력합니다.
         ft_putchar(s[i++]);
    }
}

int main(int ac, char **av)
{
    int i = 1;
    if (ac == 1)
        ft_putchar('\n');
    else
    {
        // 1부터 시작하는 모든 매개변수를 순회합니다 (우리는
        // 실행 파일의 이름을 화면에 출력하는 것을 원하지 않습니다).
        while (i < ac)
        {
            r_capitalizer(av[i]);
            ft_putchar('\n');
            i++;
        } 
    }
}
```
{% endcode %}

</details>