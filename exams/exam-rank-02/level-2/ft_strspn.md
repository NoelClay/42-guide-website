# ft\_strspn

### 주제

{% code overflow="wrap" %}
```
Assignment name	: ft_strspn
Expected files	: ft_strspn.c
Allowed functions: None
---------------------------------------------------------------

Reproduce exactly the behavior of the strspn function 
(man strspn).

The function should be prototyped as follows:

size_t	ft_strspn(const char *s, const char *accept);
```
{% endcode %}

strspn 함수의 동작을 정확하게 재현해야 합니다 (man strspn 참조).

함수는 다음과 같이 프로토타입되어야 합니다:

`size_t ft_strspn(const char *s, const char *accept);`

### Man 페이지

```
STRSPN(3) (단순화됨)

NAME
    strspn -- 문자열 범위 측정
LIBRARY
    Standard C Library (libc, -lc)
SYNOPSIS
    #include <string.h>
    size_t strspn(const char *s, const char *charset);
DESCRIPTION
    strspn() 함수는 널 종료(null-terminated) 문자열 s의 시작 부분을, 
s의 문자들이 널 종료 문자열 charset에 존재하는 한 계속해서 측정합니다. 
다시 말해, charset에 속하지 않는 s의 첫 번째 문자의 문자열 배열 인덱스를 계산하며, 
그렇지 않은 경우(s의 모든 문자가 charset에 있는 경우) 첫 번째 널 문자의 인덱스를 계산합니다.
RETURN VALUES
    strspn() 함수는 측정된 문자 수를 반환합니다.
```

### 주석 처리된 솔루션

<details>

<summary>ft_strspn.c</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdlib.h>

size_t	ft_strspn(const char *s, const char *accept)
{
	int i;
	int	j;
	int check;

	i = 0;
	while (s[i])
	{
		j = 0;
		check = 0;
		while (accept[j])
		{
			if (s[i] == accept[j])
				check = 1;
			j++;
		}
		if (check == 0)
			return (i);
		i++;
	}
	return (i);
}

/*#include <stdio.h>
int main()
{
	char *s = "hello";
	const char *accept = "jfkhpell";
	printf("%zu\n", ft_strspn(s, accept));
}*/
```
{% endcode %}

</details>