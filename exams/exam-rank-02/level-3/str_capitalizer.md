# str\_capitalizer

### 주제

```
Assignment name  : str_capitalizer
Expected files   : str_capitalizer.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a program that takes one or several strings and, for each argument,
capitalizes the first character of each word (If it's a letter, obviously),
puts the rest in lowercase, and displays the result on the standard output,
followed by a \n.

A "word" is defined as a part of a string delimited either by spaces/tabs, or
by the start/end of the string. If a word only has one letter, it must be
capitalized.

If there are no arguments, the progam must display \n.

Example:

$> ./str_capitalizer | cat -e
$
$> ./str_capitalizer "a FiRSt LiTTlE TESt" | cat -e
A First Little Test$
$> ./str_capitalizer "__SecONd teST A LITtle BiT   Moar comPLEX" "   But... This iS not THAT COMPLEX" "     Okay, this is the last 1239809147801 but not    the least    t" | cat -e
__second Test A Little Bit   Moar Complex$
   But... This Is Not That Complex$
     Okay, This Is The Last 1239809147801 But Not    The Least    T$
$>
```

### 주석 처리된 해답

<details>

<summary>str_capitalizer</summary>

{% code title="str_capitalizer.c" overflow="wrap" lineNumbers="true" %}
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

void capitalizer(char *s)
{
    int i = 0;
    while (s[i])
    {
         // 문자가 대문자라면 소문자로 만듭니다.
         if (s[i] >= 'A' && s[i] <= 'Z')
             s[i] += 32;
         // 이제 소문자인지 확인하고, 이전 문자가 공백 문자라면 다시 대문자로 만듭니다.
         if ((s[i] >= 'a' && s[i] <= 'z') && ft_isspace(s[i - 1]))
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
        // 1부터 시작하는 모든 매개변수를 순회합니다 (실행 파일의 이름이 화면에 출력되는 것을 원하지 않습니다).
        while (i < ac)
        {
            capitalizer(av[i]);
            ft_putchar('\n');
            i++;
        } 
    }
}
```
{% endcode %}

</details>