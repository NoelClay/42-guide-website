---
description: 이 페이지에서는 MiniLibX 라이브러리를 위한 몇 가지 헬퍼 함수를 소개합니다.
---

# MiniLibX 헬퍼 함수

### 간단한 put\_pixel 함수

이 함수는 `x` 및 `y` 좌표를 사용하여 화면에 픽셀을 그리는 간단한 함수입니다.

저는 [여기](https://harm-smits.github.io/42docs/libs/minilibx/getting_started.html#writing-pixels-to-a-image)에서 찾은 내용을 기반으로 이 함수를 만들었으며, 픽셀이 화면 내부에만 그려지도록 약간 수정했습니다. 이 검사 없이는 창 바깥쪽에 값을 설정하려고 시도할 경우 프로그램이 `segfault`를 일으킬 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
void ft_put_pixel(t_data *data, int x, int y, int color)
{
    char *pxl;
    
    if (x >= 0 && x < WINDOW_WIDTH && y >= 0 && y < WINDOW_HEIGHT
    {
        pxl = data->addr + (y * data->line_length + x * (data->bits_per_pixel / 8));
        *(unsigned int *)pxl = color;
    }
}
```
{% endcode %}

<details>

<summary>픽셀을 그리는 최종 코드</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include "mlx.h"
#define	WINDOW_WIDTH 1920
#define WINDOW_HEIGHT 1080

typedef struct	s_data {
	void	*img;
	char	*addr;
	int		bits_per_pixel;
	int		line_length;
	int		endian;
}				t_data;

void ft_put_pixel(t_data *data, int x, int y, int color)
{
    char *pxl;

    if (x >= 0 && x < WINDOW_WIDTH && y >= 0 && y < WINDOW_HEIGHT)
    {
        pxl = data->addr + (y * data->line_length + x * (data->bits_per_pixel / 8));
        *(unsigned int *)pxl = color;
    }
}

int	main(void)
{
	void	*mlx;
	void	*mlx_win;
	t_data	img;

	mlx = mlx_init();
	mlx_win = mlx_new_window(mlx, 1920, 1080, "Hello world!");
	img.img = mlx_new_image(mlx, 1920, 1080);
	img.addr = mlx_get_data_addr(img.img, &img.bits_per_pixel, &img.line_length,
								&img.endian);
	ft_put_pixel(&img, WINDOW_WIDTH/2, WINDOW_HEIGHT/2, 0xFF0000);
	mlx_put_image_to_window(mlx, mlx_win, img.img, 0, 0);
	mlx_loop(mlx);
}

```
{% endcode %}

자세히 살펴보시면, 이 코드는 창의 정중앙에 빨간색 픽셀을 그립니다.

</details>

### 선 그리기 함수 (약간 단순한 선 알고리즘)

MiniLibX에는 두 점 사이에 선을 그리는 함수가 없습니다. 하지만 이 기능은 `FdF`를 코딩할 때 특히 유용하며, 모든 점들을 선으로 연결해야 하므로 이 함수를 제공합니다.

{% hint style="warning" %}
이 함수는 math 라이브러리의 일부 함수를 사용하므로 프로젝트에서 이 라이브러리를 사용할 수 있는지 확인하고 포함해야 합니다(`\#include <math.h>`).
`FdF`, `Fractol`, `so-long`의 경우 모든 math 라이브러리가 허용됩니다.
{% endhint %}

{% code overflow="wrap" lineNumbers="true" %}
```c
void ft_draw_line(t_data *data, int x1, int y1, int x2, int y2, int color)
{
    int step;
    int x;
    int y;
    int i;
    int delta_x;
    int delta_y;
    
    delta_x = x2 - x1;
    delta_y = y2 - y1;
    if (abs(delta_x) >= abs(delta_y))
        step = abs(delta_x);
    else
        step = abs(delta_y);
    delta_x = delta_x / step;
    delta_y = delta_y / step;
    x = x1;
    y = x2;
    i = 0;
    while (i < step)
    {
        ft_put_pixel(data, x, y, color);
        x += delta_x;
        y += delta_y;
        i++;
    }
}
```
{% endcode %}

{% hint style="info" %}
이 함수가 Norm을 통과하도록 방법을 찾아야 합니다. 제가 어떻게 했는지는 [여기](https://github.com/Laendrun/42/blob/main/fdf/src/draw.c#L27)에서 확인하실 수 있습니다.
{% endhint %}