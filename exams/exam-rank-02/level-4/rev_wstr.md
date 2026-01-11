# rev\_wstr

### 주제

```
Assignment name  : rev_wstr
Expected files   : rev_wstr.c
Allowed functions: write, malloc, free
--------------------------------------------------------------------------------

문자열을 매개변수로 받아 그 단어들을 역순으로 출력하는 프로그램을 작성하십시오.

"단어(word)"란 공백(space) 및/또는 탭(tab), 혹은 문자열의 시작/끝으로 구분된 문자열의 일부를 의미합니다.

매개변수의 개수가 1이 아닐 경우, 프로그램은 '\n'을 표시합니다.

테스트될 매개변수에는 "추가적인" 공백은 없습니다(즉, 문자열의 시작이나 끝에 추가 공백이 없으며, 단어들은 항상 정확히 하나의 공백으로만 구분됨을 의미합니다).

예시:

$> ./rev_wstr "You hate people! But I love gatherings. Isn't it ironic?" | cat -e
ironic? it Isn't gatherings. love I But people! hate You$
$>./rev_wstr "abcdefghijklm"
abcdefghijklm
$> ./rev_wstr "Wingardium Leviosa" | cat -e
Leviosa Wingardium$
$> ./rev_wstr | cat -e
$
$>
```
### 주석 처리된 해답

<details>

<summary>rev_wstr</summary>

{% code title="rev_wstr.c" overflow="wrap" lineNumbers="true" %}
```c
#include "unistd.h"

int	main(int ac, char **av)
{
	int i;
	int j;
	int first_word;

	i = 0;
	first_word = 0;
	if (ac == 2)
	{
		while (av[1][i] != '\0')
			i++;
		i--;
		while (i >= 0)
		{
			while (i >= 0 && (av[1][i] == ' ' || av[1][i] == '\t'))
				i--;
			j = i;
			while (j >= 0 && av[1][j] != ' ' && av[1][j] != '\t')
				j--;
			if (first_word)
				write(1, " ", 1);
			write(1, &av[1][j + 1], i - j);
			first_word = 1;
			i = j;
		}
	}
	write(1, "\n", 1);
	return (0);
}
```
{% endcode %}

</details>