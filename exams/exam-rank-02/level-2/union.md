# union

### 과제

```
Assignment name  : union
Expected files   : union.c
Allowed functions: write
--------------------------------------------------------------------------------

두 개의 문자열을 인자로 받아, 두 문자열 중 어느 하나에라도 나타나는 문자들을 중복 없이 출력하는 프로그램을 작성해야 합니다.

출력은 명령줄에 문자들이 나타나는 순서대로 이루어져야 하며, 마지막에는 \n이 따라와야 합니다.

인자의 개수가 2개가 아닌 경우, 프로그램은 \n만을 출력합니다.

예시:

$>./union zpadinton "paqefwtdjetyiytjneytjoeyjnejeyj" | cat -e
zpadintoqefwjy$
$>./union ddf6vewg64f gtwthgdwthdwfteewhrtag6h4ffdhsd | cat -e
df6vewg4thras$
$>./union "rien" "cette phrase ne cache rien" | cat -e
rienct phas$
$>./union | cat -e
$
$>
$>./union "rien" | cat -e
$
$>
```

### 주석이 달린 솔루션

<details>

<summary>[union.c](union.c)</summary>

{% code title="union.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char **av)
{
    // ASCII 테이블의 모든 문자를 위한 룩업 테이블을 생성합니다.
    int i = 0, lookup[256 = {0};
    
    if (ac == 3)
    {
        // 첫 번째 문자열 전체를 순회합니다.
        // 각 문자에 대해 룩업 테이블의 값을
        // 전환(1로 설정)합니다.
        while(av[1][i])
        	lookup[(int)av[1][i++]] = 1;
        i = 0;
        // 두 번째 문자열에 대해서도 동일한 작업을 수행합니다.
        while(av[2][i])
        	lookup[(int)av[2][i++]] = 1;
        i = 0;
        
        // 첫 번째 문자열을 순회하며
        // 이전에 확인된 문자들을 화면에 작성(write)하고,
        // 출력한 후에는 룩업 테이블의 값을 다시 전환(0으로 설정)합니다.
        while (av[1][i])
        {
            if (lookup[(int)av[1][i]])
            {
                write(1, &av[1][i], 1);
                lookup[(int)av[1][i]] = 0;
            }
            i++;
        }
        i = 0;
        while (av[2][i])
        {
            if (lookup[(int)av[2][i]])
            {
                write(1, &av[2][i], 1);
                lookup[(int)av[2][i]] = 0;
            }
            i++;
        }
    }
    write(1, "\n", 1);
    return (0);
}
```
{% endcode %}

</details>