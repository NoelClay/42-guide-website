# ft\_strpbrk

이 해답은 Duc Nguyen 님이 제안했습니다 ([여기서 그의 Github 링크를 찾을 수 있습니다](https://github.com/nguyenduc-03/42Student)).

### 과제

{% code overflow="wrap" %}
```
과제 이름	: ft_strpbrk
제출할 파일	: ft_strpbrk.c
허용 함수: 없음
---------------------------------------------------------------

strpbrk 함수의 동작을 정확하게 재현하십시오
(man strpbrk를 참조하십시오).

함수는 다음과 같이 프로토타입되어야 합니다:

char	*ft_strpbrk(const char *s1, const char *s2);
```
{% endcode %}

### Man 페이지

```
STRPBRK(3) (간소화됨)

NAME
     strpbrk –- 문자열에서 다중 문자 찾기
LIBRARY
     표준 C 라이브러리 (libc, -lc)
SYNOPSIS
     #include <string.h>
     char *strpbrk(const char *s, const char *charset);

DESCRIPTION
     strpbrk() 함수는 널(null)로 끝나는 문자열 s 내에서
     charset 문자열에 포함된 문자 중 첫 번째로 나타나는 문자의 위치를 찾고,
     이 문자를 가리키는 Pointer를 반환합니다.
     만약 charset의 문자들이 s 내에 전혀 나타나지 않는다면, strpbrk()는 NULL을 반환합니다.
RETURN VALUES
     strpbrk() 함수는 문자열 내에서 발견된 임의의 문자의 첫 번째 등장 위치를
     가리키는 Pointer를 반환하며, s 내에 문자가 전혀 나타나지 않으면 NULL을 반환합니다.
```

### 주석 처리된 해답

<details>

<summary>ft_strpbrk()</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
char *ft_strpbrk(const char *s1, const char *s2)
{
    int i = 0;
    int j = 0;
    char *re = (char *)s1;

    while (s1[i])
    {
        j = 0;
        while (s2[j])
        {
            if (s1[i] == s2[j])
                return re;
            j++;
        }
        i++;
        re++;
    }
    return NULL;
}
```
{% endcode %}

</details>

#### char *re = (char *)s1;에 대한 설명

이 함수에서 변수 `re`는 입력 문자열 `s1`의 시작 부분으로 초기 설정된 임시 Pointer입니다. 이는 인덱스 (`i`)를 사용하여 `s1`을 반복하면서 현재 위치를 추적하는 데 사용됩니다. `s1`에서 다음 문자로 이동할 때마다 `re`도 함께 증가하여, 항상 `s1[i]`와 동일한 문자를 가리키도록 합니다.

`s1`의 문자가 `s2`에서 발견되면 `re`가 반환됩니다. 이는 함수가 `s1` 내에서 **일치하는 문자를 가리키는 Pointer**를 반환하도록 보장하며, 이는 `strpbrk` 계열 함수의 올바른 동작 방식입니다.