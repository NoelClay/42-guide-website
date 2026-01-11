# rev\_print

### 주제

{% code overflow="wrap" %}
```
Assignment name  : rev_print
Expected files   : rev_print.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a program that takes a string, and displays the string in reverse
followed by a newline.

If the number of parameters is not 1, the program displays a newline.

Examples:

$> ./rev_print "zaz" | cat -e
zaz$
$> ./rev_print "dub0 a POIL" | cat -e
LIOP a 0bud$
$> ./rev_print | cat -e
$
```
{% endcode %}

문자열을 인수로 받아, 해당 문자열을 역순으로 출력하고 그 뒤에 개행 문자(newline)를 출력하는 프로그램을 작성하십시오.

매개변수의 개수가 1이 아니라면, 프로그램은 개행 문자만 출력합니다.

예시는 위 내용을 참고하시기 바랍니다.

### 주석 처리된 해답

<details>

<summary>rev_print</summary>

{% code title="rev_print.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    if (ac == 2)
    {
        int i;
        
        i = 0;
        /* 문자열의 길이를 찾기 위해 전체 문자열을 반복합니다.
         */
        while (av[1][i])
            i++;
        /* 문자열의 길이만큼 (길이부터 0까지) 반복합니다.
         * 그리고 각 문자를 하나씩 작성(write)합니다.
         */
        while (i--)
            write(1, &av[1][i], 1);
    }
    write(1, "\n", 1);
}
```
{% endcode %}

</details>