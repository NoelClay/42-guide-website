# \*ft\_atoi

### 과제

{% code overflow="wrap" %}
```
Assignment name  : ft_atoi
Expected files   : ft_atoi.c
Allowed functions: None
--------------------------------------------------------------------------------

Write a function that converts the string argument str to an integer (type int)
and returns it.

It works much like the standard atoi(const char *str) function, see the man.

Your function must be declared as follows:

int	ft_atoi(const char *str);
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_atoi</summary>

{% code title="ft_atoi.c" overflow="wrap" lineNumbers="true" %}
```c
/* 이 함수는 문자가 "공백" 문자 중 하나인 경우 1을 반환하고, 그렇지 않은 경우 0을 반환합니다.
 */
int ft_isspace(int c)
{
    return ((c >= 9 && c <= 13) || c == 32 ? 1 : 0);
}

/* 이 함수는 문자가 0-9 사이의 숫자인 경우 1을 반환하고, 0-9 사이의 숫자가 아닌 경우 0을 반환합니다.
 */
int ft_isdigit(int c)
{
    return (c >= 48 && c <= 57 ? 1 : 0);
}

int ft_atoi(const char *str)
{
    int res = 0;
    int i = 0;
    int s = 1;
    
    /* 모든 공백 문자를 건너뜁니다. 
    */
    while (ft_isspace(str[i]))
        i++;
    /* 만약 '-' 부호가 있다면, 부호(sign)를 -1로 설정하고 다음 문자로 이동합니다.
     */
    if (str[i] == '-')
    {
        s = -1;
        i++;
    }
    /* 숫자가 숫자인 동안, 이를 int로 변환합니다.
     */
    while (ft_isdigit(str[i]))
    {
        res *= 10;
        res += str[i] - 48;
        i++;
    }
    /* 마지막에는, 숫자에 부호 변수(sign variable)를 곱하고 최종적으로 정수 값을 반환합니다.
     */
    return (res *= s);
}
```
{% endcode %}

</details>