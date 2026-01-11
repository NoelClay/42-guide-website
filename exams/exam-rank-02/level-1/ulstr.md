# ulstr

### 과제

{% code overflow="wrap" %}
```
Assignment name  : ulstr
Expected files   : ulstr.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a program that takes a string and reverses the case of all its letters.
Other characters remain unchanged.

You must display the result followed by a '\n'.

If the number of arguments is not 1, the program displays '\n'.

Examples :

$>./ulstr "L'eSPrit nE peUt plUs pRogResSer s'Il staGne et sI peRsIsTent VAnIte et auto-justification." | cat -e
l'EspRIT Ne PEuT PLuS PrOGrESsER S'iL STAgNE ET Si PErSiStENT vaNiTE ET AUTO-JUSTIFICATION.$
$>./ulstr "S'enTOuRer dE sECreT eSt uN sIGnE De mAnQuE De coNNaiSSanCe.  " | cat -e
s'ENtoUrER De SecREt EsT Un SigNe dE MaNqUe dE COnnAIssANcE.  $
$>./ulstr "3:21 Ba  tOut  moUn ki Ka di KE m'en Ka fe fot" | cat -e
3:21 bA  ToUT  MOuN KI kA DI ke M'EN kA FE FOT$
$>./ulstr | cat -e
$
```
{% endcode %}

**요구 사항 설명:**

문자열을 인자로 받아 그 안에 있는 모든 알파벳의 대소문자를 반전시키는 프로그램을 작성하십시오. 그 외의 문자는 변경되지 않고 유지됩니다.

결과를 출력한 후 반드시 '\n'을 붙여야 합니다.

인자의 개수가 1개가 아닐 경우, 프로그램은 '\n'만 출력해야 합니다.

### 주석 처리된 해답

<details>

<summary>ulstr</summary>

{% code title="ulstr.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    /* 인자의 개수를 확인합니다.
     */
    if (ac == 2)
    {
        int i;
        
        i = 0;
        /* 전체 문자열을 반복 처리합니다.
         */
        while (av[1][i])
        {
            /* 만약 현재 문자가 대문자라면
             * 32를 더하여 해당되는 소문자를 얻습니다.
             */
            if (av[1][i] >= 65 && av[1][i] <= 90)
                av[1][i] += 32;
            /* 만약 현재 문자가 소문자라면
             * 32를 빼서 해당되는 대문자를 얻습니다.
             */
            else if (av[1][i] >= 97 && av[1][i] <= 122)
                av[1][i] -= 32;
            /* 이후 현재 문자를 write하고
             * i를 증가시킵니다.
             */
            write(1, &av[1][i], 1);
            i++;
        }
    }
    /* 가장 마지막에, 새로운 줄(newline)을 출력합니다. 
     * 이렇게 하면 인자의 개수가 올바르지 않더라도 
     * 단일 개행 문자 하나가 출력됩니다.
     */
    write(1, "\n", 1);
}
```
{% endcode %}

</details>
