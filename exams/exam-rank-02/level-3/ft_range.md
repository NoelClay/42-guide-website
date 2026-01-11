# ft\_range

## 주제

```
Assignment name  : ft_range
Expected files   : ft_range.c
Allowed functions: malloc
--------------------------------------------------------------------------------
```

다음 함수를 작성해야 합니다:

```c
int     *ft_range(int start, int end);
```

이 함수는 `malloc()`을 사용하여 정수형 배열을 할당해야 하며, `start`에서 시작하여 `end`에서 끝나는 (start와 end를 포함하여!) 연속적인 값으로 채워야 합니다. 그리고 배열의 첫 번째 값에 대한 Pointer를 반환해야 합니다.

예시:

- (1, 3)의 경우 1, 2, 3을 포함하는 배열을 반환합니다.
- (-1, 2)의 경우 -1, 0, 1, 2를 포함하는 배열을 반환합니다.
- (0, 0)의 경우 0을 포함하는 배열을 반환합니다.
- (0, -3)의 경우 0, -1, -2, -3을 포함하는 배열을 반환합니다.

## 주석이 달린 해답

<details>

<summary>ft_range.c</summary>

{% code title="ft_range.c" overflow="wrap" lineNumbers="true" %}
```c
int *ft_range(int start, int end)
{
    int i = 0;
    // 범위의 길이를 정의합니다.
    // abs 함수를 사용할 수 없으므로,
    // 수동으로 절대값을 계산해야 합니다.
    int len = (end - start) < 0 ? ((end - start) * -1) + 1 : (end - start) + 1;
    // 필요한 범위만큼 메모리를 할당합니다.
    int *range = (int *) malloc(len * sizeof(int));
    
    // 범위를 채워 넣습니다.
    while (i < len)
    {
        // 다음 줄은 숫자가 증가하는 경우에 해당합니다.
        if (start < end)
            range[i] = start++;
        // 다음 줄은 숫자가 감소하는 경우에 해당합니다.
        else
            range[i] = start--;
        i++;
    }
    // 채워진 범위(배열)를 반환합니다.
    return (range);
}
```
{% endcode %}

</details>