# ft\_itoa

### 주제

{% code overflow="wrap" %}
```
Assignment name  : ft_itoa
Expected files   : ft_itoa.c
Allowed functions: malloc
--------------------------------------------------------------------------------

Write a function that takes an int and converts it to a null-terminated string.
The function returns the result in a char array that you must allocate.

Your function must be declared as follows:

char	*ft_itoa(int nbr);

```
int 타입의 정수를 받아 null-terminated string으로 변환하는 함수를 작성하십시오.
함수는 반드시 직접 할당(allocate)해야 하는 char 배열에 결과를 담아 반환해야 합니다.

여러분의 함수는 다음과 같이 선언되어야 합니다:

```c
char	*ft_itoa(int nbr);
```

### 주석 처리된 해답

<details>

<summary>ft_itoa</summary>

{% code title="ft_itoa.c" overflow="wrap" lineNumbers="true" %}
```c
#include "stdlib.h"

int	ft_count_len(int nbr)
{
	int	i;

	i = 0;
	if (nbr <= 0)
		i++;
	while (nbr != 0)
	{
		nbr = nbr / 10;
		i++;
	}
	return (i);
}

char	*ft_itoa(int nbr)
{
	int i;
	int len;
	long num;
	char *str;

	num = nbr;
	len = ft_count_len(nbr);
	str = (char *)malloc((sizeof(char) * (len + 1)));
	if (!str)
		return (NULL);
	str[len] = '\0';
	i = len - 1;
	if (num < 0)
	{
		str[0] = '-';
		num = -num;
	}
	while (num > 9)
	{
		str[i] = num % 10 + '0';
		num = num / 10;
		i--;
	}
	str[i] = num + '0';
	return (str);
}
```
{% endcode %}

</details>