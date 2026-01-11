# expand\_str

### 주제

```
과제 이름 : expand_str
예상 파일 : expand_str.c
허용 함수: write
--------------------------------------------------------------------------------

문자열을 인자로 받아, 각 단어 사이에 정확히 세 개의 공백을 두고 출력하는 프로그램을 작성합니다.
출력의 시작이나 끝에는 공백이나 탭이 없어야 하며, 뒤에는 개행 문자가 따라와야 합니다.

단어는 공백/탭 또는 문자열의 시작/끝으로 구분된 문자열의 섹션입니다.

만약 인자의 개수가 1이 아니거나 단어가 전혀 없다면, 단순히 개행 문자만 출력합니다.

예시:

$> ./expand_str "vous   voyez   c'est   facile   d'afficher   la   meme   chose" | cat -e
vous   voyez   c'est   facile   d'afficher   la   meme   chose$
$> ./expand_str " seulement          la c'est      plus dur " | cat -e
seulement   la   c'est   plus   dur$
$> ./expand_str "comme c'est cocasse" "vous avez entendu, Mathilde ?" | cat -e
$
$> ./expand_str "" | cat -e
$
$>
```

### 주석이 달린 해설

<details>

<summary>expand_str</summary>

{% code title="expand_str.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

void ft_putchar(char c)
{
    write(1, &c, 1);
}

int is_space(char c)
{
    if (c <= 32)
        return 1;
    return 0;
}

int main(int ac, char **av)
{
    if (ac == 2)
    {
        int i = 0, space = 0;
        // 모든 선행 공백 문자들을 건너뜁니다.
        while (is_space(av[1][i]))
            i++;
        while (av[1][i])
        {
            // 공백 문자가 있다면, space 플래그를 1로 설정합니다.
            // 공백 문자가 여러 개 있더라도, 매번 1로 설정됩니다.
            if (is_space(av[1][i]))
                 space = 1;
            // 현재 문자가 공백 문자가 아니라면
            // 플래그가 켜져 있을 때 3개의 공백을 출력합니다.
            // space 플래그를 다시 0으로 설정합니다.
            // 현재 문자를 출력합니다.
            if (!is_space(av[1][i]))
            {
                if (space)
                    write(1, "   ", 3);
                space = 0;
                ft_putchar(av[1][i]);
            }
            i++;
        }
    }
    ft_putchar('\n');
}
```
{% endcode %}

</details>