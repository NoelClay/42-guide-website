# rostring

### 주제

{% code overflow="wrap" %}
```
Assignment name  : rostring
Expected files   : rostring.c
Allowed functions: write, malloc, free
--------------------------------------------------------------------------------

Write a program that takes a string and displays this string after rotating it
one word to the left.

Thus, the first word becomes the last, and others stay in the same order.

A "word" is defined as a part of a string delimited either by spaces/tabs, or
by the start/end of the string.

Words will be separated by only one space in the output.

If there's less than one argument, the program displays \n.

Example:

$>./rostring "abc   " | cat -e
abc$
$>
$>./rostring "Que la      lumiere soit et la lumiere fut"
la lumiere soit et la lumiere fut Que
$>
$>./rostring "     AkjhZ zLKIJz , 23y"
zLKIJz , 23y AkjhZ
$>
$>./rostring "first" "2" "11000000"
first
$>
$>./rostring | cat -e
$
$>

```
문자열을 인수로 받아, 이 문자열을 한 단어만큼 왼쪽으로 회전(rotating)시킨 후 결과를 출력하는 프로그램을 작성해야 합니다.

따라서 첫 번째 단어는 마지막으로 이동하고, 나머지 단어들은 원래 순서를 유지합니다.

‘단어(word)’는 공백(spaces)/탭(tabs) 또는 문자열의 시작/끝에 의해 구분되는 문자열의 일부로 정의됩니다.

출력 시 단어들은 오직 하나의 공백으로만 구분되어야 합니다.

인수가 하나 미만인 경우, 프로그램은 `\n`을 출력해야 합니다.

예시는 위와 같습니다.

### 주석 처리된 해설

<details>

<summary>rostring</summary>

{% code title="rostring.c" overflow="wrap" lineNumbers="true" %}
```c
#include "unistd.h"

void	rostring(char *str)
{
	int	i;
	int	j;

	i = 0;
	while (str[i] && str[i] == ' ' || str[i] == '\t')
		i++;
	j = i;
	while (str[i])
	{
		while (str[i] && str[i] != ' ' && str[i] != '\t')
			i++;
		while (str[i] && (str[i] == ' ' || str[i] == '\t'))
			i++;
		while ((str[i] && (str[i] != ' ' && str[i] != '\t')) && (str[i
				- 1] == ' ' || str[i - 1] == '\t'))
		{
			while (str[i] && (str[i] != ' ' && str[i] != '\t'))
			{
				write(1, &str[i], 1);
				i++;
			}
			write(1, " ", 1);
			i++;
		}
	}
	while (str[j] && (str[j] != ' ' && str[j] != '\t'))
	{
		write(1, &str[j], 1);
		j++;
	}
}

int	main(int ac, char **av)
{
	if (ac >= 2)
	{
		rostring(av[1]);
	}
	write(1, "\n", 1);
	return (0);
}
```
{% endcode %}

</details>