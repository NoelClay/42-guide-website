# first\_word

### 주제

{% code overflow="wrap" %}
```
Assignment name  : first_word
Expected files   : first_word.c
Allowed functions: write
--------------------------------------------------------------------------------

문자열을 인자로 받아 첫 번째 단어를 출력하고, 그 뒤에 개행 문자(newline)를 표시하는 프로그램을 작성하십시오.

단어는 공백(space)/탭(tab) 문자 또는 문자열의 시작/끝으로 구분되는 문자열의 일부입니다.

매개변수의 개수가 1이 아니거나, 단어가 전혀 없는 경우에는 단순히 개행 문자만 출력합니다.

Examples:

$> ./first_word "FOR PONY" | cat -e
FOR$
$> ./first_word "this        ...    is sparta, then again, maybe    not" | cat -e
this$
$> ./first_word "   " | cat -e
$
$> ./first_word "a" "b" | cat -e
$
$> ./first_word "  lorem,ipsum  " | cat -e
lorem,ipsum$
$>
```
{% endcode %}

### 주석 처리된 해설

<details>

<summary>first_word</summary>

{% code title="first_word.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    /* 인수의 개수를 확인합니다.
     */
    if (ac == 2)
    {
        unsigned int i;
        
        i = 0;
        /* 문자열을 순회하며 시작 부분에 있을 수 있는
         * 공백(32) 및 탭(9) 문자를 제거합니다.
         */
        while (av[1][i] == 32 || av[1][i] == 9)
            i++;
        /* 그 다음, 공백, 탭 또는 널 문자(\0)를
         * 발견할 때까지 문자열의 문자를 출력하기 시작합니다.
         */
        while ((av[1][i] != 32 && av[1][i] != 9) && av[1][i])
            write(1, &av[1][i++], 1);
    }
    /* 마지막에 \n을 작성합니다. 무언가를 출력했든 안 했든
     * 모든 경우에 마지막에는 \n을 출력해야 하기 때문입니다.
     */
    write(1, "\n", 1);
    return (0);
}
```
{% endcode %}

</details>