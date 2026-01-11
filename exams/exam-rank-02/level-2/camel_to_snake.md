# camel\_to\_snake

{% code overflow="wrap" %}
```
Assignment name  : camel_to_snake
Expected files   : camel_to_snake.c
Allowed functions: malloc, realloc, write
--------------------------------------------------------------------------------

Write a program that takes a single string in lowerCamelCase format
and converts it into a string in snake_case format.

A lowerCamelCase string is a string where each word begins with a capital letter
except for the first one.

A snake_case string is a string where each word is in lower case, separated by
an underscore "_".

Examples:
$>./camel_to_snake "hereIsACamelCaseWord"
here_is_a_camel_case_word
$>./camel_to_snake "helloWorld" | cat -e
hello_world$
$>./camel_to_snake | cat -e
$
```
{% endcode %}

**과제 이름:** camel\_to\_snake
**예상 파일:** camel\_to\_snake.c
**허용 함수:** malloc, realloc, write
--------------------------------------------------------------------------------

단일 문자열을 lowerCamelCase 형식으로 받아서 snake\_case 형식의 문자열로 변환하는 프로그램을 작성합니다.

lowerCamelCase 문자열은 첫 번째 단어를 제외하고 각 단어가 대문자로 시작하는 문자열입니다.

snake\_case 문자열은 각 단어가 소문자이며 밑줄("_")로 구분된 문자열입니다.

예시:
$>./camel\_to\_snake "hereIsACamelCaseWord"
here\_is\_a\_camel\_case\_word
$>./camel\_to\_snake "helloWorld" | cat -e
hello\_world$
$>./camel\_to\_snake | cat -e
$

### 주석이 달린 솔루션

<details>

<summary>camel_to_snake</summary>

{% code title="camel_to_snake.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    int i;
    
    if (ac == 2)
    {
        i = 0;
        /* 전체 문자열을 순회합니다
         */
        while (av[1][i])
        {
            /* 대문자를 만나는 경우,
             * 해당 문자를 소문자로 만들고 그 앞에 _를 작성해야 합니다
             */
            if (av[1][i] >= 65 && av[1][i] <= 90)
            {
                /* 여기서, 대문자를
                 * 해당 소문자로 변경합니다
                 */
                av[1][i] += 32;
                /* 화면에 _를 작성합니다
                 */
                write(1, "_", 1);
            }
            /* 그 다음, 변경되었든 아니든 현재 문자를 작성합니다
             */
            write(1, &av[1][i], 1);
            i++;
        }
    }
    /* 마지막으로, 개행 문자를 작성합니다
     */
    write(1, "\n", 1);
}
```
{% endcode %}

</details>