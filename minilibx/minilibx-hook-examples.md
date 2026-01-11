---
description: "이 페이지에서는 MiniLibX와 함께 사용할 수 있는 몇 가지 Hook 예시를 보여드립니다."
---

# MiniLibX Hook 예시

{% hint style="info" %}
여기서 사용된 `keycode`와 `mousecode`는 macOS 기준입니다. Linux에서는 다릅니다 (Mac의 Escape 키는 53이지만, Linux에서는 65307이며 Windows에서는 아마도 또 다를 것입니다).
{% endhint %}

MiniLibX의 Hook이 무엇인지 더 자세히 알고 싶다면, [이벤트](https://harm-smits.github.io/42docs/libs/minilibx/events.html) 및 [Hook](https://harm-smits.github.io/42docs/libs/minilibx/hooks.html) 챕터에서 추가 정보를 찾아보실 수 있습니다.

아래에서는 가장 중요한 값들과 공식 문서에 설명되어 있지 않은 일부 값들을 보여주는 몇 가지 Hook 예시를 제시하겠습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
#include "mlx.h"
#define WINDOW_HEIGHT 720
#define WINDOW_WIDTH 1280
int main(void)
{
/* 
 * 이것은 MLX 초기화 부분입니다. 아래에서 제가 수행하는 작업을
 * 더 잘 보여주기 위해 예시에 포함시켰습니다.
 */
    t_env *env;

    env.mlx = mlx_init();
    env.win = mlx_new_window(env.mlx, WINDOW_WIDTH, WINDOW_HEIGHT, WINDOW_NAME);
    env.img = mlx_new_image(env.mlx, WINDOW_WIDTH, WINDOW_HEIGHT);
    env.addr = mlx_get_data_addr(env.img, &env.bits_per_pixel, &env.line_length, &env.endian);
     /* MLX 초기화 끝 */
     /* 여기에서 Hook들을 선언합니다. 아래에서 구현부를 확인하십시오.
     */
     mlx_hook(env.win, 4, 0, mouse_handler, &env);
     // mouse_handler는 마우스 다운(mouse down) 이벤트가 발생할 때마다 호출됩니다.
     mlx_hook(env.win, 2, 1L << 0, key_handler, &env);
     // key_handler는 키가 눌러질 때마다 호출됩니다.
     mlx_hook(env.win, 17, 1L << 0, close_window, &env);
     // close_window는 창을 닫기 위해 빨간색 X 버튼을 클릭했을 때 호출됩니다.
     mlx_loop_hook(env.mlx, render, &env);
     // MLX는 계속해서 루프를 돌기 때문에, mlx_loop_hook을 사용하여
     // MLX가 루프를 돌 때마다 함수를 실행할 수 있습니다.
     
     mlx_loop(env.mlx);
}
```
{% endcode %}

### 간단한 키 눌림 처리기 (key\_pressed\_handler)

`keypress` 또는 `keyrelease` 이벤트에 대해 수신할지 여부를 필터링할 수 있으므로, 두 개의 다른 키 핸들러를 가질 수 있습니다. 예를 들어, `space` 키를 눌렀을 때 특정 작업을 시작하고, 키에서 손을 뗄 때만 작업을 중지하도록 설정할 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
int key_handler(int keycode, t_env *env)
{
    if (keycode == 53)
        ft_printf("ESCAPE");
    else if (keycode == 0 || keycode == 123)
        ft_printf("LEFT (A / ARROW_LEFT)");
    else if (keycode == 2 || keycode == 124)
        ft_printf("RIGHT (D / ARROW_RIGHT)");
    else if (keycode == 1 || keycode == 125)
        ft_printf("DOWN (S / ARROW_DOWN)");
    else if (keycode == 13 || keycode == 126)
        ft_printf("LEFT (A / ARROW_LEFT)");
    else
        ft_printf("%d\n", keycode);
    return (0);
}
```
{% endcode %}

<figure><img src="../.gitbook/assets/keycode.png" alt=""><figcaption><p>키 코드</p></figcaption></figure>

{% hint style="info" %}
이 함수를 살펴보면 키보드의 모든 키에 해당하는 `keycode`를 어떻게 찾을 수 있는지 이해하실 수 있을 것입니다.
{% endhint %}

### 간단한 마우스 처리기 (mouse\_handler)

{% code overflow="wrap" lineNumbers="true" %}
```c
int mouse_handler(int mousecode, int x, int y, t_env *env)
{
    /* x 및 y 매개변수는 이벤트가 발생했을 때
     * 창 내에서 마우스가 위치한 픽셀 좌표입니다.
     * 이 좌표를 사용하여 사용자가 창의 특정 영역을 클릭했는지
     * 확인할 수 있습니다.
     */
     if (mousecode == 1)
         ft_printf("Left Click");
     else if (mousecode == 2)
         ft_printf("Right Click");
     else if (mousecode == 3)
         ft_printf("Middle Click");
     else if (mousecode == 4)
         ft_printf("Scroll UP");
     else if (mousecode == 5)
         ft_printf("Scroll DOWN");
     else if (mousecode == 6)
         ft_printf("Scroll right");
     else if (mousecode == 7)
         ft_printf("Scroll left");
     else
         ft_printf("%d\n", mousecode);
}
```
{% endcode %}

### mlx\_loop\_hook

이 Hook은 MLX 루프를 돌 때마다 함수를 호출하며, 이를 사용하여 화면에 표시되는 내용을 업데이트할 수 있습니다. 그렇기 때문에 저는 이 함수를 대부분 `render` 또는 `draw`라고 부릅니다.

예를 들어, `so-long` 프로젝트에서 저는 이 Hook을 사용하여 변경된 내용을 기반으로 맵을 그립니다. 배경을 완전히 다시 그려서 기존 내용을 덮어쓰고, 그 후에 벽과 플레이어를 다시 그립니다. 플레이어 위치는 `keypressed` 이벤트에 따라 변경될 수 있으므로 플레이어가 이동합니다. 수집품(collectibles)의 경우도 마찬가지입니다. 이미 수집한 아이템을 다시 그리고 싶지 않습니다 (제가 설명한 예시는 [여기](https://github.com/Laendrun/42/blob/main/so_long/src/main.c#L43)에서 확인하실 수 있습니다).

```c
int render(t_env *env)
{
    draw_background(env);
    draw_map(env);
    draw_player(env);
    draw_moves(env);
}
```

{% hint style="info" %}
이 내용들은 단지 예시이자 간단한 핸들러이며, 카뎃들이 이 작동 방식을 더 잘 이해하고 필요에 따라 코드를 조정할 수 있도록 돕기 위함입니다.
{% endhint %}
