# repeat\_alpha

### 주제

{% code overflow="wrap" %}
```
과제 이름      : repeat_alpha
요구 파일      : repeat_alpha.c
허용 함수      : write
--------------------------------------------------------------------------------

문자열을 인수로 받아, 알파벳 문자를 해당 문자의 알파벳 순서(index)만큼 반복하여 출력하는 repeat_alpha라는 프로그램을 작성합니다. 출력 후에는 개행 문자를 추가해야 합니다.

'a'는 'a'가 되고, 'b'는 'bb'가 되며, 'e'는 'eeeee'가 되는 식입니다.

대소문자는 변경되지 않고 유지됩니다.

인수의 개수가 1개가 아니라면, 단순히 개행 문자만 출력합니다.

예시:

$>./repeat_alpha "abc"
abbccc
$>./repeat_alpha "Alex." | cat -e
Alllllllllllleeeeexxxxxxxxxxxxxxxxxxxxxxxx.$
$>./repeat_alpha 'abacadaba 42!' | cat -e
abbacccaddddabba 42!$
$>./repeat_alpha | cat -e
$
$>
$>./repeat_alpha "" | cat -e
$
$>
```
{% endcode %}

### 주석이 달린 솔루션

<details>

<summary>repeat_alpha</summary>

{% code title="repeat_alpha" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    int i;
    int j;
    
    /* 인수의 개수를 확인합니다
     */
    if (ac == 2)
    {
        i = 0;
        /* 전체 문자열을 순회합니다
         */
        while (av[1][i])
        {
            /* 현재 문자가 대문자 알파벳이라면
             */
            if (av[1][i] >= 65 && av[1][i] <= 90)
            {
                j = 0;
                /* j가 현재 문자의 알파벳 순서보다 작을 때까지 반복합니다
                 * - 64는 알파벳 순서를 얻기 위함입니다
                 * ASCII에서 A는 65이므로, 65 - 64 = 1 입니다
                 * ASCII에서 Z는 90이므로, 90 - 64 = 26 입니다
                 */
                while (j < av[1][i] - 64)
                {
                    write(1, &av[1][i], 1);
                    j++;
                }
            }
            else if (av[1][i] >= 97 && av[1][i] <= 122)
            {
                j = 0;
                /* j가 현재 문자의 알파벳 순서보다 작을 때까지 반복합니다
                 * - 96은 알파벳 순서를 얻기 위함입니다
                 * ASCII에서 a는 97이므로, 97 - 96 = 1 입니다
                 * ASCII에서 z는 122이므로, 122 - 96 = 26 입니다
                 */
                while (j < av[1][i] - 96)
                {
                    write(1, &av[1][i], 1);
                    j++;
                }
            }
            else
                /* 현재 문자가 알파벳이 아니라면
                 * 단순히 문자를 한 번 출력합니다
                 */
                write(1, &av[1][i], 1);
            i++;
        }
    }
    write(1, "\n", 1);
}
```
{% endcode %}

</details>