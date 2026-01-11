# alpha\_mirror

### 과제 내용

{% code overflow="wrap" %}
```
Assignment name  : alpha_mirror
Expected files   : alpha_mirror.c
Allowed functions: write
--------------------------------------------------------------------------------

alpha_mirror라는 프로그램을 작성합니다. 이 프로그램은 문자열 하나를 인수로 받아, 각 알파벳 문자를 반대편의 알파벳 문자로 치환한 후, 이 문자열을 출력하고 마지막에 개행 문자(newline)를 출력해야 합니다.

'a'는 'z'가 되고, 'Z'는 'A'가 됩니다.
'd'는 'w'가 되고, 'M'은 'N'이 됩니다.

이와 같이 작동합니다.

대소문자는 변경되지 않습니다.

만약 인수의 개수가 1이 아니라면, 개행 문자만 출력합니다.

예시:

$>./alpha_mirror "abc"
zyx
$>./alpha_mirror "My horse is Amazing." | cat -e
Nb slihv rh Znzarmt.$
$>./alpha_mirror | cat -e
$
$>
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>alpha_mirror</summary>

{% code title="alpha_mirror.c" overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(int ac, char *av[])
{
    int i;
    
    if (ac == 2)
    {
        i = 0;
        while (av[1][i])
        {
            if (av[1][i] >= 65 && av[1][i] <= 90)
            /* 우리는 해당 문자의 ASCII 코드 값을 가장 마지막 대문자(Z)의 ASCII 코드 값에서 뺀 다음, 가장 첫 번째 대문자(A)의 ASCII 코드 값을 더합니다.
             * 90 - 65(A) = 25, 25 + 65 = 90(Z)
             * 90 - 66(B) = 24, 24 + 65 = 89(Y)
             */ 
                av[1][i] = 90 - av[1][i] + 65;
            else if (av[1][i] >= 97 && av[1][i] <= 122)
            /* 우리는 해당 문자의 ASCII 코드 값을 가장 마지막 소문자(z)의 ASCII 코드 값에서 뺀 다음, 가장 첫 번째 소문자(a)의 ASCII 코드 값을 더합니다.
             * 122 - 97(a) = 25, 25 + 97 = 122(z)
             * 122 - 98(b) = 24, 24 + 97 = 121(y)
             */ 
                av[1][i] = 122 - av[1][i] + 97;
            /* 마지막으로, 현재 문자를 화면에 출력합니다.
             */
            write(1, &av[1][i], 1);
            i++;
        }
    }
    write(1, "\n", 1);
}
```
{% endcode %}

</details>