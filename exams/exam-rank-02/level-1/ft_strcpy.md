# \*ft\_strcpy

### 주제

{% code overflow="wrap" %}
```
Assignment name  : ft_strcpy
Expected files   : ft_strcpy.c
Allowed functions:
--------------------------------------------------------------------------------

Reproduce the behavior of the function strcpy (man strcpy).

Your function must be declared as follows:

char    *ft_strcpy(char *s1, char *s2);
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>ft_strcpy</summary>

{% code title="ft_strcpy.c" overflow="wrap" lineNumbers="true" %}
```c
// man strcpy=> char *strcpy(char *dest, const char *src)

char    *ft_strcpy(char *s1, char *s2)
{
    unsigned int i;
    
    i = 0;
    /* 소스 문자열 (s2)을 순회합니다.
     * 만약 소스가 비어있다면, 루프에 진입할 필요조차 없습니다.
     */
    while (s2[i])
    {
        /* 목적지 (s1)의 i 인덱스에 있는 문자를
         * 소스 (s2)의 i 인덱스에 있는 문자와 같도록 설정합니다.
         */
        s1[i] = s2[i];
        i++;
    }
    /* 목적지 (s1)의 i 인덱스에 있는 문자를 0으로 설정합니다.
     * 이것은 복사된 내용이 있는 경우 목적지 (s1)를 NUL-terminate하기 위해 수행됩니다.
     * 또는 소스 (s2)가 비어있는 경우 목적지 (s1)를 비어있도록 설정하기 위해 수행됩니다.
     */
    s1[i] = 0;
    return (s1);
}
```
{% endcode %}

</details>

{% hint style="success" %}
수정을 도와준 Veronique에게 감사합니다 :smile:
{% endhint %}