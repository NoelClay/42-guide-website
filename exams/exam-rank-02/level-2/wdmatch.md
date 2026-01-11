# wdmatch

### 주제

```
Assignment name  : wdmatch
Expected files   : wdmatch.c
Allowed functions: write
--------------------------------------------------------------------------------
```

두 개의 문자열을 인수로 받아, 두 번째 문자열에 나타나는 문자의 순서를 존중하면서, 두 번째 문자열의 문자를 사용하여 첫 번째 문자열을 만들 수 있는지 확인하는 프로그램을 작성하십시오.

만들 수 있다면, 프로그램은 해당 문자열을 표시하고 그 뒤에 $\texttt{\n}$을 출력합니다. 그렇지 않다면 단순히 $\texttt{\n}$만 표시합니다.

인수의 개수가 2개가 아니라면, 프로그램은 $\texttt{\n}$을 표시합니다.

예시:

```
$>./wdmatch "faya" "fgvvfdxcacpolhyghbreda" | cat -e
faya$
$>./wdmatch "faya" "fgvvfdxcacpolhyghbred" | cat -e
$
$>./wdmatch "quarante deux" "qfqfsudf arzgsayns tsregfdgs sjytdekuoixq " | cat -e
quarante deux$
$>./wdmatch "error" rrerrrfiiljdfxjyuifrrvcoojh | cat -e
$
$>./wdmatch | cat -e
$
```

### 주석이 달린 솔루션

<details>

<summary>wdmatch.c</summary>

{% code title="wdmatch.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char **av)
{
    if (ac == 3)
    {
        const char *s1 = av[1];
        const char *s2 = av[2];
        int len = 0, i = 0;
        
        while (s1[len])
            len++;
        
        // s2에서 s1의 모든 문자를 확인합니다.
        while (i < len && *s2)
        {
            // 다음 줄은 현재 s1의 문자가
            // s2의 문자와 같은지 확인합니다.
            // 비교가 완료된 후, 조건이 일치하지 않더라도
            // s2 Pointer를 증가시킵니다.
            // 만약 조건이 일치하면, 다음 s1 문자를 확인하기 위해 i를 증가시킵니다.
            if (s1[i] == *s2++)
                i++;
        }
        if (i == len)
            write(1, s1, len);
    }
    write(1, "\n", 1);
    return (0);
}
```
{% endcode %}

</details>