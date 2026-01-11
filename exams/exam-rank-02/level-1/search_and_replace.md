# search\_and\_replace

### 주제

{% code overflow="wrap" %}
```
Assignment name  : search_and_replace
Expected files   : search_and_replace.c
Allowed functions: write, exit
--------------------------------------------------------------------------------

Write a program called search_and_replace that takes 3 arguments, the first
arguments is a string in which to replace a letter (2nd argument) by
another one (3rd argument).

If the number of arguments is not 3, just display a newline.

If the second argument is not contained in the first one (the string)
then the program simply rewrites the string followed by a newline.

Examples:
$>./search_and_replace "Papache est un sabre" "a" "o"
Popoche est un sobre
$>./search_and_replace "zaz" "art" "zul" | cat -e
$
$>./search_and_replace "zaz" "r" "u" | cat -e
zaz$
$>./search_and_replace "jacob" "a" "b" "c" "e" | cat -e
$
$>./search_and_replace "ZoZ eT Dovid oiME le METol." "o" "a" | cat -e
ZaZ eT David aiME le METal.$
$>./search_and_replace "wNcOre Un ExEmPle Pas Facilw a Ecrirw " "w" "e" | cat -e
eNcOre Un ExEmPle Pas Facile a Ecrire $
```
{% endcode %}

**번역:**

search\_and\_replace라는 프로그램을 작성하십시오. 이 프로그램은 3개의 인수를 받으며, 첫 번째 인수는 문자열입니다. 이 문자열에서 두 번째 인수로 주어진 문자를 세 번째 인수로 주어진 다른 문자로 대체해야 합니다.

인수의 개수가 3개가 아닌 경우, 단순히 개행 문자(newline)를 표시합니다.

두 번째 인수가 첫 번째 문자열에 포함되어 있지 않은 경우, 프로그램은 단순히 해당 문자열을 다시 출력한 다음 개행 문자를 출력합니다.

예시:
$>./search\_and\_replace "Papache est un sabre" "a" "o"
Popoche est un sobre
$>./search\_and\_replace "zaz" "art" "zul" | cat -e
$
$>./search\_and\_replace "zaz" "r" "u" | cat -e
zaz$
$>./search\_and\_replace "jacob" "a" "b" "c" "e" | cat -e
$
$>./search\_and\_replace "ZoZ eT Dovid oiME le METol." "o" "a" | cat -e
ZaZ eT David aiME le METal.$
$>./search\_and\_replace "wNcOre Un ExEmPle Pas Facilw a Ecrirw " "w" "e" | cat -e
eNcOre Un ExEmPle Pas Facile a Ecrire $

---

### 주석 처리된 솔루션

<details>

<summary>search_and_replace</summary>

{% code title="search_and_replace.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    if (ac == 4)
    {
        int i;
        
        i = 0;
        /* 두 번째 및 세 번째 인수가 오직 하나의 문자로만 구성된 경우에만 전체 문자열을 반복합니다. */
        while (av[1][i])
        {
            /* 현재 문자가 대체해야 할 문자인 경우, 세 번째 인수로 대체합니다. */
            if (av[1][i] == av[2][0])
                av[1][i] = av[3][0];
            /* 그리고 현재 문자를 출력합니다. */
            write(1, &av[1][i], 1);
            i++;
        }
    }
    /* 맨 마지막에 개행 문자를 출력합니다. 이렇게 하면 인수가 충분하든 아니든 상관없이 항상 개행 문자를 출력하게 됩니다. */
    write(1, "\n", 1);
}
```
{% endcode %}

</details>
