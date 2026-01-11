# \*ft\_strdup

### 주제

{% code overflow="wrap" %}
```
Assignment name  : ft_strdup
Expected files   : ft_strdup.c
Allowed functions: malloc
--------------------------------------------------------------------------------

Reproduce the behavior of the function strdup (man strdup).

Your function must be declared as follows:

char    *ft_strdup(char *src);
```
{% endcode %}

함수 `strdup`의 동작을 재현합니다 (man strdup을 참조하세요).

여러분의 함수는 다음과 같이 선언되어야 합니다:

`char    *ft_strdup(char *src);`

### 주석 처리된 해답

<details>

<summary>ft_strdup()</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdlib.h>

char    *ft_strdup(char *src)
{
	char	*tmp; //이 변수에 문자열을 복제할 것입니다.
	int	len; 
	int	i;

	len = 0;
	i = 0;
	while (src[len]) //충분한 메모리를 확보하기 위해 src의 길이를 계산합니다.
		len++;
	tmp = malloc(sizeof(char) * len + 1); //malloc을 사용하여 공간을 할당합니다. + 1은 문자열 끝에 추가되는 "\0"를 위한 공간이 필요하기 때문입니다 (잊지 마세요!).
	if (!tmp) //malloc 보호 처리
		return (NULL);
	while (src[i]) //새로운 문자열을 완성하기 위한 루프
	{
		tmp[i] = src[i];
		i++;
	}
	tmp[i] = 0; //끝에 널 문자(\0)를 추가합니다.
	return (tmp);
}


#include <stdio.h>

int main()
{
	char	*a;
	a = "Hello World";
	printf("%s\n", ft_strdup(a));
}
```
{% endcode %}

</details>
