# ft\_strcspn

### 과제

{% code overflow="wrap" %}
```
Assignment name	: ft_strcspn
Expected files	: ft_strcspn.c
Allowed functions: None
---------------------------------------------------------------

Reproduce exactly the behavior of the function strcspn
(man strcspn).

The function should be prototyped as follows:

size_t	ft_strcspn(const char *s, const char *reject);
```
{% endcode %}

### Man 페이지

```
STRCSPN(3) (simplified)

NAME
    strcspn -- span a string
LIBRARY
    Standard C Library (libc, -lc)
SYNOPSIS
    #include <string.h>
    size_t strcspn(const char *s, const char *charset);
DESCRIPTION
    The strcspn() function spans the initial part of the null-terminated
string s as long as the characters from s do not occur in the null-
terminated string charset (it spans the complement of the charset). In
other words, it computes the string array index of the first character
of s which is also in charset, else the index of the first null character.
RETURN VALUES
    The strcspn() functions return the number of characters spanned.
```

### 주석 처리된 해설

이 문제에 대해서는 두 가지 다른 예시를 제공해 드립니다. 첫 번째는 문자열 `s`와 `reject`에서 index를 사용하는 방식이며, 두 번째 예시는 Pointer를 직접 사용하는 방식입니다.

<details>

<summary>Index 방식</summary>

{% code title="ft_strcspn.c" overflow="wrap" lineNumbers="true" %}
```c
// size_t 타입을 위해 필요합니다.
#include <stdio.h>

size_t ft_strcspn(const char *s, const char *reject)
{
    size_t i;
    size_t j;
    
    i = 0;
    // 전체 문자열을 반복합니다.
    while(s[i++])
    {
        j = 0;
        // 현재 문자열 문자를 charset의 모든 문자와 비교하여 확인합니다.
        while (reject[j])
        {
            // 문자를 찾으면, 현재 문자열의 index를 반환합니다.
            if (s[i] == reject[j++])
            	return (i);
        }
    }
    // 만약 전체 문자열을 확장(span)했다면, i를 반환합니다. 
    // 이 경우 찾은 거부 문자(rejected char)는 NULL 종료 문자입니다.
    return (i);
}
```
{% endcode %}

</details>

<details>

<summary>Pointer 방식</summary>

{% code title="ft_strcspn.c" overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>

size_t ft_strcspn(const char *s, const char *reject)
{
    // s의 시작 부분에 대한 Pointer를 저장합니다.
    const char *og = s;
    const char *s2;
    
    // 전체 문자열을 반복합니다. 
    while (*s++)
    {
        // s2를 reject의 시작 위치로 설정합니다.
        // 이렇게 하여 reject Pointer를 원래 위치에 유지합니다.
        // 그 다음 전체 charset을 반복합니다.
        s2 = reject;
        while (*s2)
        {
            // 만약 현재 문자열 문자가 charset에서 발견되면,
            // 확장된 문자(span)의 개수를 반환합니다.
            if (*s == *s2++)
                // 여기서 우리는 확장된 문자 수를 계산하기 위해 원본 Pointer를 사용합니다.
                return (s - og);
        }
    }
    // 위와 마찬가지로, 확장된 문자 수를 계산하기 위해 원본 Pointer를 사용합니다.
    return (s - og);
}
```
{% endcode %}

</details>