# sort\_int\_tab

### 주제

{% code overflow="wrap" %}
```
Assignment name  : sort_int_tab
Expected files   : sort_int_tab.c
Allowed functions:
--------------------------------------------------------------------------------

다음 함수를 작성합니다:

void sort_int_tab(int *tab, unsigned int size);

이 함수는 정확히 'size'개의 요소를 포함하는 'tab' int array를 오름차순으로 (in-place, 즉 제자리에서) 정렬해야 합니다.

중복된 값(Doubles)은 보존되어야 합니다.

입력은 항상 일관적입니다.

```
### 주석 처리된 해답

<details>

<summary>sort_int_tab</summary>

{% code title="sort_int_tab.c" overflow="wrap" lineNumbers="true" %}
```c
void	sort_int_tab(int *tab, unsigned int size)
{
	int tmp;
	unsigned int i;
	unsigned int j;

	i = 0;
	while (i < size - 1)
	{
		j = i;
		while (j < size)
		{
			if (tab[i] > tab[j])
			{
				tmp = tab[i];
				tab[i] = tab[j];
				tab[j] = tmp;
			}
			j++;
		}
		i++;
	}
}

```
{% endcode %}

</details>