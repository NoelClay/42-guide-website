# epur\_str

### 주제

```
과제 이름  : epur_str
요구 파일   : epur_str.c
허용 함수: write
--------------------------------------------------------------------------------

문자열을 인수로 받아, 단어 사이에 정확히 하나의 공백만을 두고, 문자열의 시작이나 끝에는 공백이나 탭이 없도록 이 문자열을 출력하는 프로그램을 작성해야 합니다. 출력 후에는 \n이 따라와야 합니다.

"단어(word)"는 공백/탭, 또는 문자열의 시작/끝으로 구분되는 문자열의 일부로 정의됩니다.

인수의 개수가 1이 아니거나, 출력할 단어가 없는 경우, 프로그램은 \n을 출력합니다.

예시:

$> ./epur_str "vous voyez c'est facile d'afficher la meme chose" | cat -e
vous voyez c'est facile d'afficher la meme chose$
$> ./epur_str " seulement          la c'est      plus dur " | cat -e
seulement la c'est plus dur$
$> ./epur_str "comme c'est cocasse" "vous avez entendu, Mathilde ?" | cat -e
$
$> ./epur_str "" | cat -e
$
$>
```

### 주석 처리된 솔루션

<details>

<summary>epur_str</summary>

{% code title="epur_str.c" overflow="wrap" lineNumbers="true" %}
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
        // 모든 선행 공백 문자를 건너뜁니다.
        while (is_space(av[1][i]))
            i++;
        while (av[1][i])
        {
            // 공백 문자가 있다면, space 플래그를 1로 설정합니다.
            // 여러 개의 공백이 있더라도, 플래그는 매번 1로
            // 설정될 것입니다.
            if (is_space(av[1][i]))
                space = 1;
            // 현재 문자가 공백 문자가 아니라면
            // 플래그가 켜져 있다면 공백을 출력합니다.
            // space 플래그를 다시 0으로 설정합니다.
            // 현재 문자를 출력합니다.
            if (!is_space(av[1][i]))
            {
                if (space)
                    ft_putchar(' ');
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
