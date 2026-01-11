# \*ft\_strcmp

### 과제

{% code overflow="wrap" %}
```
Assignment name  : ft_strcmp
Expected files   : ft_strcmp.c
Allowed functions:
--------------------------------------------------------------------------------

Reproduce the behavior of the function strcmp (man strcmp).

Your function must be declared as follows:

int    ft_strcmp(char *s1, char *s2);
```
{% endcode %}

### Man Page

```
STRCMP(3) (simplified)

NAME
    strcmp -- compare strings
SYNOPSIS
    #include <string.h>
    int strcmp(const char *s1, const char *s2);
DESCRIPTION
    The strcmp() and strncmp() functions lexicographically compare the null-
terminated strings s1 and s2.
RETURN VALUES
     The strcmp() and strncmp() functions return an integer greater than,
 equal to, or less than 0, according as the string s1 is greater than,
 equal to, or less than the string s2.  The comparison is done using
 unsigned characters, so that ‘\200’ is greater than ‘\0’.
```

### 주석이 달린 해답

<details>

<summary>ft_strcmp</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
int ft_strcmp(char *s1, char *s2)
{
    int i;
    
    i = 0;
    // 두 문자열이 모두 같고
    // 둘 중 적어도 하나가 아직 끝나지 않은 동안 루프를 실행합니다.
    while ((s1[i] || s2[i]) && s1[i] == s2[i])
        i++;
    // 더 이상 루프를 실행할 수 없을 때,
    // 두 문자열에서 마지막으로 비교된 문자의 뺄셈 결과를 반환합니다.
    // 이 결과는 음수, 0, 또는 양수 값이 됩니다.
    return (s1[i] - s2[i]);
}
```
{% endcode %}

</details>