# ft\_strrev

### 주제 (Subject)

{% code overflow="wrap" %}
```
Assignment name  : ft_strrev
Expected files   : ft_strrev.c
Allowed functions:
--------------------------------------------------------------------------------

Write a function that reverses (in-place) a string.

It must return its parameter.

Your function must be declared as follows:

char    *ft_strrev(char *str);
```
{% endcode %}

과제 이름: ft\_strrev
필요한 파일: ft\_strrev.c
허용되는 함수:

문자열을 (제자리에서, in-place) 뒤집는 함수를 작성하십시오.

이 함수는 매개변수를 반환해야 합니다.

귀하의 함수는 다음과 같이 선언되어야 합니다:

`char *ft_strrev(char *str);`

### 주석 처리된 해답 (Commented solution)

<details>

<summary>ft_strrev.c</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>

char    *ft_strrev(char *str)
{
	int i;
	int len;
	char tmp;


	i = 0;
	len = 0;
	while (str[len])
		len++;
	while (i < len - 1)
	{
		tmp = str[i];
		str[i] = str[len - 1];
		str[len - 1] = tmp;
		i++;
		len--;
	}
	return (str);
}

/*int	main(void)
{
	char s[] = "Hello World";
	ft_strrev(s);
	printf("%s\n", s);
	return (0);
}*/
```
{% endcode %}

</details>