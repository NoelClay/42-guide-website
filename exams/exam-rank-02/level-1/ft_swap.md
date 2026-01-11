# ft\_swap

### 과제

{% code overflow="wrap" %}
```
Assignment name  : ft_swap
Expected files   : ft_swap.c
Allowed functions:
--------------------------------------------------------------------------------

Write a function that swaps the contents of two integers the adresses of which
are passed as parameters.

Your function must be declared as follows:

void	ft_swap(int *a, int *b);
```
{% endcode %}

매개변수로 주소가 전달된 두 개의 int형 정수 내용을 서로 바꾸는 함수를 작성하십시오.

여러분의 함수는 다음과 같이 선언되어야 합니다:

`void ft_swap(int *a, int *b);`

### 주석 처리된 해답

<details>

<summary>ft_swap</summary>

{% code title="ft_swap.c" overflow="wrap" lineNumbers="true" %}
```c
void ft_swap(int *a, int *b)
{
    /* 두 변수의 내용을 바꾸기 위해서는
     * 임시 변수를 선언해야 합니다.
     */
    int x;
    
    // 임시 변수가 *a의 값과 같도록 설정합니다.
    x = *a;
    // *a의 값이 *b의 값과 같도록 설정합니다.
    *a = *b;
    // *b의 값이 x의 값과 같도록 설정합니다.
    *b = x;
}
```
{% endcode %}

</details>
