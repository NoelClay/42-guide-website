# flood\_fil

이러한 종류의 문제를 다뤄본 적이 없다면, 이 원리를 **매우 잘** 설명하는 영상을 시청하시는 것을 추천합니다!

바로 이 영상을 보신 후에는 flood fill을 5분 안에 코딩할 수 있을 것입니다 ;)

{% embed url="https://www.youtube.com/watch?v=VuiXOc81UDM" %}

다음은 과제 설명입니다:

### 과제

{% code overflow="wrap" %}
```
Assignment name  : flood_fill
Expected files   : *.c, *.h
Allowed functions: -
--------------------------------------------------------------------------------
Write a function that takes a char ** as a 2-dimensional array of char, a 
t_point as the dimensions of this array and a t_point as the starting point.
Starting from the given 'begin' t_point, this function fills an entire zone 
by replacing characters inside with the character 'F'. A zone is an group of 
the same character delimitated horizontally and vertically by other characters
or the array boundry.
The flood_fill function won't fill diagonally.
The flood_fill function will be prototyped like this:
  void  flood_fill(char **tab, t_point size, t_point begin);
The t_point structure is prototyped like this:
  typedef struct  s_point
  {
    int           x;
    int           y;
  }               t_point;
Example:
$> cat test_main.c
#include "test_functions.h"
#include "flood_fill.h"
int main(void)
{
	char **area;
	t_point size = {8, 5};
	t_point begin = {2, 2};
	char *zone[] = {
		"1 1 1 1 1 1 1 1",
		"1 0 0 0 1 0 0 1",
		"1 0 0 1 0 0 0 1",
		"1 0 1 1 0 0 0 1",
		"1 1 1 0 0 0 0 1",
	}
	area = make_area(zone);
	print_tab(area);
	flood_fill(area, size, begin);
	putc('\n');
	print_tab(area);
	return (0);
}
$> gcc flood_fill.c test_main.c test_functions.c -o flood_fill; ./flood_fill
1 1 1 1 1 1 1 1
1 0 0 0 1 0 0 1
1 0 0 1 0 0 0 1
1 0 1 0 0 0 0 1
1 1 0 0 0 0 0 0
1 1 1 1 1 1 1 1
1 F F F 1 0 0 1
1 F F 1 0 0 0 1
1 F 1 0 0 0 0 1
1 1 0 0 0 0 0 0
$> 
------------------------------------------------------------------------------*/
```

### 주석이 달린 솔루션

<details>
<summary>flood_fill</summary>

{% code title="flood_fill.c" overflow="wrap" lineNumbers="true" %}
```c
#include "flood_fill.h"

// 2차원 문자 배열 영역을 flood fill 하는 재귀 함수입니다.
void fill(char **tab, t_point size, char target, int row, int col)
{
    // 현재 행과 열 값이 경계를 벗어났는지 확인합니다.
    if (row < 0 || col < 0 || row >= size.y || col >= size.x)
        return;
    
    // 현재 셀이 이미 채워졌거나(F) 목표 문자와 일치하지 않는지 확인합니다.
    if (tab[row][col] == 'F' || tab[row][col] != target)
        return;

    // 현재 셀을 채워진 상태로 표시합니다.
    tab[row][col] = 'F';

    // 인접 셀을 재귀적으로 채웁니다.
    fill(tab, size, target, row -1, col); // 위쪽 셀을 채웁니다.
    fill(tab, size, target, row +1, col); // 아래쪽 셀을 채웁니다.
    fill(tab, size, target, row, col - 1); // 왼쪽 셀을 채웁니다.
    fill(tab, size, target, row, col + 1); // 오른쪽 셀을 채웁니다.
}

// 지정된 지점에서 flood fill을 시작하는 함수입니다.
void flood_fill(char **tab, t_point size, t_point begin)
{
    char target = tab[begin.y][begin.x]; // 채워야 할 목표 문자를 가져옵니다.
    fill(tab, size, target, begin.y, begin.x); // 지정된 지점에서 flood fill을 시작합니다.
}
```
{% endcode %}

</details>

이제 과제의 예시를 사용하여 모든 작업을 올바르게 수행했는지 확인할 수 있습니다 :-)