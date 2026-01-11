# rotone

### 주제

{% code overflow="wrap" %}
```
과제 이름        : rotone
요구되는 파일    : rotone.c
허용되는 함수    : write
--------------------------------------------------------------------------------

문자열을 인자로 받아, 그 문자열을 표시하는 프로그램을 작성하십시오. 이때 각 문자는 알파벳 순서상 다음 문자로 대체됩니다.

'z'는 'a'가 되고 'Z'는 'A'가 됩니다. 대소문자는 영향을 받지 않고 유지됩니다.

출력 뒤에는 반드시 \n이 따라와야 합니다.

인자의 개수가 1이 아닌 경우, 프로그램은 \n만 표시합니다.

예시:

$>./rotone "abc"
bcd
$>./rotone "Les stagiaires du staff ne sentent pas toujours tres bon." | cat -e
Mft tubhjbjsft ev tubgg of tfoufou qbt upvkpvst usft cpo.$
$>./rotone "AkjhZ zLKIJz , 23y " | cat -e
BlkiA aMLJKa , 23z $
$>./rotone | cat -e
$
$>
$>./rotone "" | cat -e
$
$>
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>rotone</summary>

{% code title="rotone.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    if (ac == 2)
    {
        int i;
        
        i = 0;
        /* 문자열 전체를 순회합니다.
         */
        while (av[1][i])
        {
            /* 문자가 Z 또는 z인지 확인합니다.
             * 이 경우, A 또는 a를 얻기 위해 25를 뺍니다.
             * (각각 순환 처리합니다.)
             */
            if (av[1][i] == 90 || av[1][i] == 122)
                av[1][i] -= 25;
            /* 문자가 Aa부터 Yy 사이에 있는지 확인합니다.
             * 이 경우, 다음 문자를 얻기 위해 1을 더할 수 있습니다.
             */
            else if ((av[1][i] >= 65 && av[1][i] <= 89) || (av[1][i] >= 95 && av[1][i] <= 121))
                av[1][i] += 1;
            /* 마지막으로, 문자를 출력(write)하고
             * 카운터를 증가시킵니다.
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