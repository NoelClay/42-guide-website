# snake\_to\_camel

### 과제

```
Assignment name  : snake_to_camel
Expected files   : snake_to_camel.c
Allowed functions: malloc, free, realloc, write
--------------------------------------------------------------------------------

Write a program that takes a single string in snake_case format
and converts it into a string in lowerCamelCase format.

A snake_case string is a string where each word is in lower case, separated by
an underscore "_".

A lowerCamelCase string is a string where each word begins with a capital letter
except for the first one.

Examples:
$>./snake_to_camel "here_is_a_snake_case_word"
hereIsASnakeCaseWord
$>./snake_to_camel "hello_world" | cat -e
helloWorld$
$>./snake_to_camel | cat -e
$
```

### 주석 처리된 솔루션

{% code title="snake_to_camel.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int toUpper(int c)
{
    if (c >= 'a' && c <= 'z')
        return (c - 32);
    return (c);
}

int ft_putchar(char c)
{
    return write(1, &c, 1);
}

int main(int ac, char **av)
{
    int i;
    
    // 인자가 하나만 있는 경우에만 작업을 수행합니다.
    if (ac == 2)
    {
        i = 0;
        // 전체 문자열을 반복 처리합니다.
        while (av[1][i])
        {
            // 밑줄('_')을 만나면,
            // 다음 문자를 대문자로 만들고 밑줄은 출력하지 않아야 합니다.
            // 따라서 대문자 문자만 출력합니다.
            if (av[1][i] == '_')
                // 여기서 ++i 연산자를 사용하여
                // 값에 접근하기 전에 i를 증가시킵니다.
                // 이렇게 하면 밑줄을 건너뛸 수 있습니다.
                ft_putchar(toUpper(av[1][++i]));
            else
                // 그 외의 모든 경우에는, 단순히
                // 문자를 화면에 출력합니다.
                ft_putchar(av[1][i]);
            i++;
        }
    }
    // 그리고 마지막으로, 개행 문자를 출력합니다.
    // 여기에 두면 모든 경우에
    // 개행 문자가 하나만 필요하게 됩니다.
    ft_putchar('\n');
}
```
{% endcode %}