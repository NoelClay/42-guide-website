# fprime

### 주제

```
과제 이름      : fprime
예상 파일      : fprime.c
허용 함수      : printf, atoi
--------------------------------------------------------------------------------

양의 int를 입력받아 해당 숫자의 소인수를 표준 출력(standard output)에 표시하고, 그 뒤에 개행 문자(newline)를 출력하는 프로그램을 작성하십시오.

소인수들은 오름차순으로 표시되어야 하며, 출력된 식이 올바른 결과를 제공하도록 '*' 문자로 구분되어야 합니다.

매개변수(parameter)의 개수가 1개가 아니라면, 단순히 개행 문자만 표시하십시오.

입력 값이 존재하는 경우, 해당 입력은 유효한 값이라고 가정합니다.

예시:

$> ./fprime 225225 | cat -e
3*3*5*5*7*11*13$
$> ./fprime 8333325 | cat -e
3*3*5*5*7*11*13*37$
$> ./fprime 9539 | cat -e
9539$
$> ./fprime 804577 | cat -e
804577$
$> ./fprime 42 | cat -e
2*3*7$
$> ./fprime 1 | cat -e
1$
$> ./fprime | cat -e
$
$> ./fprime 42 21 | cat -e
$
```
### 주석 처리된 해답

<details>

<summary>fprime</summary>

{% code title="fprime.c" overflow="wrap" lineNumbers="true" %}
```c
#include "stdio.h"
#include "stdlib.h"

int	main(int ac, char **av)
{
	int	i;
	int	num;

	i = 2;
	if (ac == 2)
	{
		num = atoi(av[1]);
		if (num == 1)
			printf("1");
		while (i <= num)
		{
			if (num % i == 0)
			{
				printf("%d", i);
				if (num != i)
					printf("*");
				num = num / i;
			}
			else
				i++;
		}
	}
	printf("\n");
	return (0);
}
```
{% endcode %}

</details>