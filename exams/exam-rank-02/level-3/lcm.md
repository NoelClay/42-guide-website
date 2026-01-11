# lcm

### 주제

```
Assignment name  : lcm
Expected files   : lcm.c
Allowed functions:
--------------------------------------------------------------------------------
```

두 개의 unsigned int를 매개변수로 받아 그 매개변수들의 계산된 LCM을 반환하는 함수를 작성하십시오.

두 개의 0이 아닌 정수에 대한 LCM (Lowest Common Multiple, 최소 공배수)은 두 정수 모두로 나누어떨어지는 가장 작은 양의 정수입니다.

LCM은 두 가지 방법으로 계산할 수 있습니다:

- 0이 아닌 공통 배수가 나올 때까지 각 정수의 모든 배수를 계산할 수 있습니다.

- 두 정수의 HCF (Highest Common Factor, 최대 공약수)를 사용하여 다음과 같이 계산할 수 있습니다:

	$$LCM(x, y) = | x * y | / HCF(x, y)$$

  $$| x * y |$$는 "x와 y의 곱의 절댓값"을 의미합니다.

만약 정수 중 최소한 하나가 0(null)이라면, LCM은 0과 같습니다.

여러분의 함수는 다음과 같이 프로토타입되어야 합니다:

```c
  unsigned int    lcm(unsigned int a, unsigned int b);
```

### 주석이 달린 해결책

<details>

<summary>lcm</summary>

{% code title="lcm.c" overflow="wrap" lineNumbers="true" %}
```c
unsigned int lcm(unsigned int a, unsigned int b)
{
    unsigned int g = (a > b) ? a : b;
    
    // 정수 중 하나라도 0인지 확인합니다.
    if (a == 0 || b == 0)
    	return (0);
    
    while (1)
    {
    	// 만약 g가 a와 b 모두로 완벽하게 나누어떨어진다면
    	// 이것이 LCM입니다.
        if ((g % a == 0) && (g % b == 0))
            return (g);
        g++;
    }
}
// 테스트를 위해 다음 부분을 주석 해제하십시오.
// #include <stdio.h>
// #include <stdlib.h>
// int main(int ac, char **av)
// {
// 	if (ac == 3)
// 	{
// 		unsigned int m = lcm(atoi(av[1]), atoi(av[2]));
// 		printf("LCM: %u\n", m);
// 	}
// }
```
{% endcode %}

</details>
