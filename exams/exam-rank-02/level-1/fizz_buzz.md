# fizz\_buzz

### 과제 내용

{% code overflow="wrap" %}
```
Assignment name  : fizzbuzz
Expected files   : fizzbuzz.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a program that prints the numbers from 1 to 100, each separated by a
newline.

If the number is a multiple of 3, it prints 'fizz' instead.

If the number is a multiple of 5, it prints 'buzz' instead.

If the number is both a multiple of 3 and a multiple of 5, it prints 'fizzbuzz' instead.

Example:

$>./fizzbuzz
1
2
fizz
4
buzz
fizz
7
8
fizz
buzz
11
fizz
13
14
fizzbuzz
[...]
97
98
fizz
buzz
$>
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>fizz_buzz</summary>

{% code title="fizz_buzz.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

/* 이 과제에서는 숫자를 다루고 출력해야 하므로,
 * 간단한 putnbr 함수가 필요합니다.
 */
void ft_putnbr(int i)
{
    /* 만약 숫자가 9보다 크다면, 숫자를 10으로 나눈 값으로
     * 함수를 다시 호출합니다. 이렇게 하면 숫자 끝의 한 자리를
     * 제거할 수 있습니다 (예: 123 => 12).
     */
    if (i > 9)
        ft_putnbr(i / 10);
    /* 그 다음, 0부터 9까지의 모든 숫자가 포함된 문자열에서
     * i % 10 (예: 123 => 3)에 해당하는 인덱스의 문자를 출력합니다.
     */
    write(1, &"0123456789"[i % 10], 1);
    /* 위에서 작성한 내용에 대해 설명하겠습니다:
     * C에서 '문자열(string)'은 존재하지 않으며, 문자열을 저장할 때는
     * NUL 문자로 끝나는 문자 배열을 저장합니다.
     * 따라서 위에서 제가 한 일은 문자열을 작성한 다음,
     * 대괄호([ ])를 사용하여 특정 인덱스를 선택함으로써
     * 배열 요소를 선택하는 것과 동일한 작업을 수행한 것입니다.
     * write 함수의 두 번째 인수는 char * 타입입니다.
     * 이것이 제가 문자열 앞에 & 문자를 추가한 이유입니다.
     * 이렇게 함으로써 write() 함수에 제가 출력하려는
     * 특정 문자에 대한 Pointer를 제공하게 됩니다.
     */
}

int main(void)
{
    int i;
    
    i = 1;
    while (i <= 100)
    {
        if (i % 3 == 0 && i % 5 == 0)
            write(1, "fizzbuzz", 8);
        else if  (i % 3 == 0)
            write(1, "fizz", 4);
        else if (i % 5 == 0)
            write(1, "buzz", 4);
        else
            ft_putnbr(i);
        i++;
        write(1, "\n", 1);
    }
}
```
{% endcode %}

</details>
