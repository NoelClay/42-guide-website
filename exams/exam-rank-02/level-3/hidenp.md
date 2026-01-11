# hidenp

### 주제

```
Assignment name  : hidenp
Expected files   : hidenp.c
Allowed functions: write
--------------------------------------------------------------------------------

hidenp라는 이름의 프로그램을 작성합니다. 이 프로그램은 두 개의 문자열을 인자로 받으며,
첫 번째 문자열이 두 번째 문자열 안에 숨겨져 있으면 1과 개행 문자를 출력하고,
그렇지 않으면 0과 개행 문자를 출력합니다.

s1과 s2가 문자열이라고 가정합니다. s1이 s2 안에 '숨겨져 있다'는 것은 s1의 각 문자가
s1에 나타나는 순서와 동일한 순서로 s2에서 발견될 수 있다는 의미입니다.
또한, 빈 문자열은 모든 문자열 안에 숨겨져 있는 것으로 간주합니다.

만약 인자의 개수가 2개가 아니라면, 프로그램은 개행 문자(\n)를 출력합니다.

예시 :

$>./hidenp "fgex.;" "tyf34gdgf;'ektufjhgdgex.;.;rtjynur6" | cat -e
1$
$>./hidenp "abc" "2altrb53c.sse" | cat -e
1$
$>./hidenp "abc" "btarc" | cat -e
0$
$>./hidenp | cat -e
$
$>
```

### 주석 처리된 해답

<details>

<summary>hidenp</summary>

{% code title="hidenp.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char **av)
{
    int i = 0, j = 0;
    
    if (ac == 3)
    {
        // s1에 내용이 있는 경우에만 s2를 반복 처리합니다.
        while (av[2][j] && av[1][i])
        {
            // s2의 현재 문자가 s1의 현재 문자와 같다면,
            // s1에서의 위치를 다음으로 진행합니다.
            if (av[2][j] == av[1][i])
                i++;
            // s2에서는 매번 위치를 진행합니다.
            j++;
        }
        // s1의 끝에 도달했다면, s1은 s2 안에 숨겨져 있는 것입니다.
        if (av[1][i] == 0)
            write(1, "1", 1);
        else
            write(1, "0", 1);
    }
    write(1, "\n", 1);
}
```
{% endcode %}

</details>