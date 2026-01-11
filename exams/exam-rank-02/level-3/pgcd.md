# pgcd

### 주제

```
Assignment name  : pgcd
Expected files   : pgcd.c
Allowed functions: printf, atoi, malloc, free
--------------------------------------------------------------------------------

int 범위 내에 맞는, 두 개의 엄격히 양수인 정수를 나타내는 두 문자열을 인자로 받는 프로그램을 작성합니다.

해당 숫자들의 최대공약수를 출력하고, 그 뒤에 개행 문자를 출력합니다 (최대공약수는 항상 엄격히 양수인 정수입니다).

매개변수의 개수가 2개가 아닌 경우, 개행 문자를 출력합니다.

예시:

$> ./pgcd 42 10 | cat -e
2$
$> ./pgcd 42 12 | cat -e
6$
$> ./pgcd 14 77 | cat -e
7$
$> ./pgcd 17 3 | cat -e
1$
$> ./pgcd | cat -e
$
```

### 주석 처리된 해답

<pre class="language-c" data-overflow="wrap" data-line-numbers><code class="lang-c"><strong>#include &#x3C;stdio.h>
</strong><strong>#include &#x3C;stdlib.h>
</strong><strong>
</strong><strong>int main(int ac, char **av)
</strong>{

    if (ac == 3)
    {
        // 전체 내용을 모두 설명하지는 않겠습니다.
	// 하지만 14와 77을 입력으로 받는다면
	// 다음과 같은 과정을 거칩니다 (각 반복은 세미콜론으로 구분됩니다).
	// 14; 14; 14; 14; 14; 14; 7
	// 77; 63; 49; 35; 21; 7;  7
        int number1 = atoi(av[1]);
        int number2 = atoi(av[2]);
        
        if (number1 > 0 &#x26;&#x26; number2 > 0)
<strong>        {
</strong><strong>            while (number1 != number2)
</strong><strong>            {
</strong><strong>                if (number1 > number2)
</strong><strong>                    number1 = number1 - number2;
</strong><strong>                else
</strong><strong>                    number2 = number2 - number1;
</strong><strong>            }
</strong><strong>            printf("%d", number1);
</strong><strong>        }
</strong>    }
    printf("\n");
}
</code></pre>