# inter

### 주제

{% code overflow="wrap" %}
```
Assignment name  : inter
Expected files   : inter.c
Allowed functions: write
--------------------------------------------------------------------------------

두 개의 문자열을 인수로 받아, 두 문자열 모두에 나타나는 문자를 중복 없이 표시하는 프로그램을 작성합니다.
이때 문자는 첫 번째 문자열에 나타나는 순서대로 출력되어야 합니다.

출력 후에는 \n이 따라와야 합니다.

인수의 개수가 2개가 아닌 경우, 프로그램은 \n을 출력합니다.

예시:

$>./inter "padinton" "paqefwtdjetyiytjneytjoeyjnejeyj" | cat -e
padinto$
$>./inter ddf6vewg64f gtwthgdwthdwfteewhrtag6h4ffdhsd | cat -e
df6ewg4$
$>./inter "rien" "cette phrase ne cache rien" | cat -e
rien$
$>./inter | cat -e
$
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>inter.c</summary>

{% code title="inter.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int	ft_putchar(char c);

int	main(int ac, char **av)
{
	int	i = 0, j;
	// lookup table 역할을 할 int 배열을 생성합니다.
	// ASCII 테이블 때문에 크기는 256이며, 각 요소는
	// 단일 문자에 대응됩니다.
	int	lookup[256] = {};

	if (ac == 3)
	{
		// 전체 문자열에 대해 반복합니다.
		while (av[1][i])
		{
			j = 0;
			// 두 번째 문자열의 모든 문자와
			// 비교합니다.
			while (av[2][j])
			{
				// 문자들이 동일한지 확인하며
				// 또한 lookup table에서 해당 문자가
				// 이미 발견된 문자가 아닌지 확인합니다.
				if (av[1][i] == av[2][j] && !lookup[(int)av[2][j]])
				{
					// 해당 문자가 발견되었음을
					// lookup table에 표시합니다.
					lookup[(int)av[2][j]] = 1;
					// 해당 문자를
					// 화면에 출력합니다.
					ft_putchar(av[2][j]);
				}
				j++;
			}
			i++;
		}
	}
	ft_putchar('\n');
}

int	ft_putchar(char c)
{
	return write(1, &c, 1);
}
```
{% endcode %}

</details>