# ft\_rrange

### 과제

```
과제 이름      : ft_rrange
제출 예상 파일 : ft_rrange.c
허용 함수      : malloc
--------------------------------------------------------------------------------

다음 함수를 작성합니다:

int     *ft_rrange(int start, int end);

이 함수는 (malloc()을 사용하여) 정수 배열을 할당해야 하며, end 값에서 시작하여 start 값으로 끝나는 연속적인 값으로 배열을 채워야 합니다 (start와 end 값을 모두 포함해야 합니다!). 그 후, 배열의 첫 번째 값에 대한 Pointer를 반환해야 합니다.

예시:

- (1, 3)이 주어지면 3, 2, 1을 포함하는 배열을 반환합니다.
- (-1, 2)이 주어지면 2, 1, 0, -1을 포함하는 배열을 반환합니다.
- (0, 0)이 주어지면 0을 포함하는 배열을 반환합니다.
- (0, -3)이 주어지면 -3, -2, -1, 0을 포함하는 배열을 반환합니다.
```

### 주석이 달린 해답

<details>

<summary>ft_rrange()</summary>

{% code title="ft_rrange.c" overflow="wrap" lineNumbers="true" %}
```c
int *ft_rrange(int start, int end)
{
    int i = 0;
    // 범위의 길이를 정의합니다.
    // abs 함수에 접근할 수 없으므로,
    // 수동으로 절댓값을 계산해야 합니다.
    int len = (end - start) < 0 ? ((end - start) * -1) + 1 : (end - start) + 1;
    // 필요한 범위를 할당합니다.
    int *range = (int *) malloc(len * sizeof(int));
    
    // 범위에 값을 채웁니다.
    while (i < len)
    {
        // 다음 줄은 숫자가 증가하는 경우입니다.
        if (end < start)
            range[i] = end++;
        // 다음 줄은 숫자가 감소하는 경우입니다.
        else
            range[i] = end--;
        i++;
    }
    // 채워진 범위를 반환합니다.
    return (range);
}
```
{% endcode %}

</details>