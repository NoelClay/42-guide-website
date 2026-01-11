# \*ft\_strlen

### 주제

{% code overflow="wrap" %}
```
Assignment name  : ft_strlen
Expected files   : ft_strlen.c
Allowed functions:
--------------------------------------------------------------------------------

Write a function that returns the length of a string.

Your function must be declared as follows:

int	ft_strlen(char *str);
```
{% endcode %}

### 주석 처리된 해설

<details>

<summary>ft_strlen</summary>

저는 추가적인 설명을 덧붙일 필요가 없다고 생각합니다. 최소한 여러분이 이것이 어떻게 작동하는지 알고 있기를 바랍니다.

{% code title="ft_strlen.c" overflow="wrap" lineNumbers="true" %}
```c
int ft_strlen(char *str)
{
    int i;
    
    i = 0;
    while (str[i])
        i++;
    return (i);
}
```
{% endcode %}

</details>