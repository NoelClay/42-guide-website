# rot\_13

### 과제

{% code overflow="wrap" %}
```
Assignment name  : rot_13 (과제 이름: rot_13)
Expected files   : rot_13.c (예상 파일: rot_13.c)
Allowed functions: write (허용된 함수: write)
--------------------------------------------------------------------------------

문자열을 인자로 받아, 알파벳 순서에서 13칸 앞에 있는 문자로 각각의 문자를 대체하여 출력하는 프로그램을 작성합니다.

'z'는 'm'이 되고 'Z'는 'M'이 됩니다. 대소문자는 영향을 받지 않고 유지됩니다.

출력 후에는 개행 문자가 따라옵니다.

인자의 개수가 1개가 아닌 경우, 프로그램은 개행 문자를 출력합니다.

예시:

$>./rot_13 "abc"
nop
$>./rot_13 "My horse is Amazing." | cat -e
Zl ubefr vf Nznmvat.$
$>./rot_13 "AkjhZ zLKIJz , 23y " | cat -e
NxwuM mYXVWm , 23l $
$>./rot_13 | cat -e
$
$>
$>./rot_13 "" | cat -e
$
$>
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>rot_13</summary>

{% code title="rot_13.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    if (ac == 2)
    {
        int i;
        
        i = 0;
        /* 전체 문자열을 순회합니다.
         */
        while (av[1][i])
        {
            /* 문자가 Aa - Mm 사이에 있는지 확인합니다.
             * 이 경우, 13을 더하여
             * 13칸 앞에 있는 문자를 얻을 수 있습니다.
             */
            if ((av[1][i] >= 65 && av[1][i] <= 77) || (av[1][i] >= 97 && av[1][i] <= 109))
                av[1][i] += 13;
            /* 문자가 Nn - Zz 사이에 있는지 확인합니다.
             * 이 경우, 13을 빼서
             * 13칸 앞에 있는 문자를 얻을 수 있습니다.
             */
            else if ((av[1][i] >= 78 && av[1][i] <= 90) || (av[1][i] >= 110 && av[1][i] <= 122))
                av[1][i] -= 13;
            /* 마지막으로, 문자를 쓰고
             * 카운터를 증가시킬 수 있습니다.
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