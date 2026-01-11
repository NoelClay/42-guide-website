# ft\_is\_power\_2

### 주제

{% code overflow="wrap" %}
```
Assignment name  : is_power_of_2
Expected files   : is_power_of_2.c
Allowed functions: None
--------------------------------------------------------------------------------

Write a function that determines if a given number is a power of 2.

This function returns 1 if the given number is a power of 2, otherwise it returns 0.

Your function must be declared as follows:

int	    is_power_of_2(unsigned int n);
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_is_power_2.c</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
int	    is_power_of_2(unsigned int n)
{

// test 변수를 1로 초기화하고, 매개변수(n)로 받은 변수와 같거나 커질 때까지 2를 계속 곱합니다. 두 변수가 같다면 (계속 2를 곱해왔기 때문에) 이는 2의 거듭제곱임을 의미합니다.
	
	int test = 1;

	while (test <= n)
	{
		if (test == n)
			return  (1); // test는 2의 거듭제곱입니다.
		test = test * 2;
	}
	// test 변수가 n보다 커지면 루프를 종료합니다. 이는 2의 거듭제곱이 아니라는 뜻이므로 0을 반환해야 합니다.
	return (0);
}


#include <stdio.h>
int main()
{
	printf("%d", is_power_of_2(16));
}
```
{% endcode %}

</details>