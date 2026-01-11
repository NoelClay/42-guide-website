# \*ft\_putstr

### 과제

{% code overflow="wrap" %}
```
Assignment name  : ft_putstr
Expected files   : ft_putstr.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a function that displays a string on the standard output.

The pointer passed to the function contains the address of the string's first
character.

Your function must be declared as follows:

void	ft_putstr(char *str);
```
{% endcode %}

표준 출력(standard output)에 문자열을 출력하는 함수를 작성하십시오.

함수에 전달되는 Pointer는 해당 문자열의 첫 번째 문자 주소를 포함합니다.

귀하의 함수는 다음과 같이 선언되어야 합니다:

`void ft_putstr(char *str);`

### 주석 처리된 해답

<details>

<summary>ft_putstr</summary>

{% code title="ft_putstr.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

void ft_putstr(char *str)
{
    // 부호 없는 정수형 변수 'i'를 정의하고 0으로 초기화합니다.
    unsigned int i = 0;

    // 'str'이 가리키는 문자열에서 널 문자 ('\0')가 나올 때까지 반복합니다.
    while (str[i])
    {
        // 'write' 함수를 호출하여 문자열의 현재 위치에 있는 문자(변수 'i'로 지정됨)를
        // 표준 출력(stdout)으로 출력합니다.
        write(1, &str[i], 1);

        // 각 반복 후 'i'를 1씩 증가시킵니다.
        i++;
    }
}
```
{% endcode %}

</details>