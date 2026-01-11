# do\_op

### 지침

{% code overflow="wrap" %}
```
Assignment name  : do_op
Expected files   : *.c, *.h
Allowed functions: atoi, printf, write
--------------------------------------------------------------------------------

Write a program that takes three strings:
- The first and the third one are representations of base-10 signed integers
  that fit in an int.
- The second one is an arithmetic operator chosen from: + - * / %

The program must display the result of the requested arithmetic operation,
followed by a newline. If the number of parameters is not 3, the program
just displays a newline.

You can assume the string have no mistakes or extraneous characters. Negative
numbers, in input or output, will have one and only one leading '-'. The
result of the operation fits in an int.

Examples:

$> ./do_op "123" "*" 456 | cat -e
56088$
$> ./do_op "9828" "/" 234 | cat -e
42$
$> ./do_op "1" "+" "-43" | cat -e
-42$
$> ./do_op | cat -e
$
```
{% endcode %}

**번역:**

세 개의 문자열을 인수로 받는 프로그램을 작성하십시오:
- 첫 번째 문자열과 세 번째 문자열은 `int`에 저장 가능한 10진수 부호 있는 정수를 나타냅니다.
- 두 번째 문자열은 다음 산술 연산자 중 하나여야 합니다: `+ - * / %`

프로그램은 요청된 산술 연산의 결과를 표시해야 하며, 그 뒤에 개행 문자(newline)를 출력해야 합니다. 만약 매개변수의 개수가 3개가 아니라면, 프로그램은 단순히 개행 문자만 표시해야 합니다.

입력 문자열에는 실수나 불필요한 문자가 없다고 가정할 수 있습니다. 입력 또는 출력에서 음수는 오직 하나의 선행 '-'만을 가집니다. 연산 결과는 `int`에 저장 가능합니다.

### 주석 달린 해답

<details>

<summary>do_op</summary>

`switch` 문을 이해하려면 이 페이지를 확인하십시오 : (todo)

{% code title="do_op.c" overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <stdlib.h>

int main(int ac, char *av[])
{
    /* 인수의 개수를 확인합니다
     */
    if (ac == 4)
    {
        /* 그런 다음 연산자(av[2][0])에 대해 switch 문을 실행합니다
         */
        switch(av[2][0])
        {
            /* 만약 연산자가 +라면 간단히 덧셈을 수행하고
             * 그 결과를 화면에 출력합니다
             */
            case '+':
                printf("%d", atoi(av[1]) + atoi(av[3]));
                break;
            /* 만약 연산자가 -라면 간단히 뺄셈을 수행하고
             * 그 결과를 화면에 출력합니다
             */
            case '-':
                printf("%d", atoi(av[1]) - atoi(av[3]));
                break;
            /* 만약 연산자가 *라면 간단히 곱셈을 수행하고
             * 그 결과를 화면에 출력합니다
             */
            case '*':
                printf("%d", atoi(av[1]) * atoi(av[3]));
                break;
            /* 만약 연산자가 /라면 간단히 나눗셈을 수행하고
             * 그 결과를 화면에 출력합니다
             */            
            case '/':
                printf("%d", atoi(av[1]) / atoi(av[3]));
                break;
            /* 만약 연산자가 %라면 간단히 나머지 연산(modulo)을 수행하고
             * 그 결과를 화면에 출력합니다
             */
            case '%':
                printf("%d", atoi(av[1]) % atoi(av[3]));
                break;
        }
    }
    /* 마지막으로, 개행 문자(newline)를 출력합니다
     */
    printf("\n");
}
```
{% endcode %}

</details>