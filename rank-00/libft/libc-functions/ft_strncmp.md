# ft\_strncmp

### 주제

{% code overflow="wrap" %}
```
STRNCMP(3) (간소화됨)

NAME
    strncmp -- 문자열 비교
SYNOPSIS
    int strncmp(const char *s1, const char *s2, size_t n);
DESCRIPTION
    strncmp() 함수는 널 종료(null-terminated) 문자열 s1과 s2를 사전식(lexicographically)으로 비교합니다.
    strncmp() 함수는 최대 n개의 문자까지만 비교합니다. strncmp()는 이진 데이터(binary data)가 아닌 문자열을 비교하도록 설계되었기 때문에, '\0' 문자 뒤에 나타나는 문자는 비교되지 않습니다.
RETURN VALUES
    strncmp() 함수는 문자열 s1이 문자열 s2보다 크거나, 같거나, 작은지에 따라 0보다 크거나, 같거나, 작은 정수를 반환합니다. 이 비교는 부호 없는 문자(unsigned characters)를 사용하여 수행되므로, '\200'은 '\0'보다 큰 것으로 간주됩니다.
```
{% endcode %}

### 이해하기 쉬운 설명

`strncmp()`는 문자열을 사전식 순서(lexicographic order)로 비교합니다. 이는 각 문자를 해당하는 ASCII 값을 기준으로 비교한다는 것을 의미합니다.

`strncmp()`는 두 문자열에서 최대 `n`개의 문자까지만 비교합니다.

반환 값은 발견된 차이에 따라 달라집니다.
만약 두 문자열이 같다면, 차이가 없으므로 반환 결과는 `0`이 됩니다.

차이가 발견되었을 때, `s2`의 첫 번째 다른 문자가 `s1`의 같은 위치에 있는 문자보다 크다면, 반환 결과는 음수(negative)가 됩니다.

차이가 발견되었을 때, `s2`의 첫 번째 다른 문자가 `s1`의 같은 위치에 있는 문자보다 작다면, 반환 결과는 양수(positive)가 됩니다.

### 힌트

{% code title="ft_strncmp.c" overflow="wrap" lineNumbers="true" %}
```c
int ft_strncmp(const char *s1, const char *s2, size_t n)
{
    /* n개의 문자에 도달할 때까지 두 문자열을 반복합니다. */
    /* 현재 s1의 문자가 현재 s2의 문자와 다른지 확인합니다.
     * 
     */
         /* 문자들이 다르다면, 두 문자의 차이를 반환합니다.
          * 
          */
      /* n개의 문자까지 두 문자열을 읽었지만 차이가 발견되지 않았다면,
       * 차이가 없으므로 0을 반환합니다.
       */
}
```
{% endcode %}

### 주석이 달린 해답

<details>

<summary>ft_strncmp</summary>

{% code title="ft_strncmp.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int ft_strncmp(const char *s1, const char *s2, size_t n)
{
    size_t i;
    
    i = 0;
    while ((s1[i] || s2[i]) && i < n)
    {
        if ((unsigned char) s1[i] != (unsigned char) s2[i])
            return ((unsigned char) s1[i] - (unsigned char) s2[i]);
        i++;
    }
    return (0);
}
```
{% endcode %}

</details>
