# 🖌️ MiniLibX

### 소개 (Introduction)

이 페이지에서는 MiniLibX에 대한 정보를 다룹니다. MiniLibX는 (최소한) 랭크 2의 세 가지 그래픽 프로젝트(so\_long, FdF, Fract-ol)에 사용될 라이브러리입니다.

MiniLibX를 사용하는 데 필요한 대부분의 정보는 이 [페이지](https://harm-smits.github.io/42docs/libs/minilibx/getting_started.html)에서 찾았습니다.

온라인에서 다른 문서를 많이 찾지 못했습니다. 이 문서에는 기본적인 작업을 수행하는 방법에 대한 몇 가지 예제가 포함되어 있지만, 저는 그 설명 중 일부를 약간 개선하고 MiniLibX 작동 방식을 더 잘 이해하려고 노력했을 때 정말 필요했던 몇 가지 예제를 더 추가하고 싶습니다.

### 설치 (Installation)

{% hint style="warning" %}
저는 학교 컴퓨터에서 MiniLibX를 사용하여 프로젝트의 상당 부분을 진행했습니다. 따라서 Windows나 Linux를 사용하고 계시다면, 여러분의 머신에 정확하게 설치하려면 이 [링크](https://harm-smits.github.io/42docs/libs/minilibx/getting_started.html#installation)를 확인해 주십시오.
{% endhint %}

다음은 제가 so\_long 프로젝트를 위해 사용했던 Makefile입니다 (실제로 사용했던 Makefile에서 약간 각색했습니다).

{% code title="Makefile" overflow="wrap" lineNumbers="true" %}
```
NAME = so_long
SRC = $(addprefix src/, main.c utils.c draw.c map_parser.c path_checker.c game_utils.c map_parser_utils.c free.c)
GNL_SRC = $(addprefix gnl/, gnl.c gnl_utils.c)
PRINTF_SRC = $(addprefix ft_printf/, ft_print_c.c ft_print_d.c ft_print_p.c ft_print_s.c ft_print_u.c ft_print_x.c ft_printf.c)

OBJ := $(SRC:%.c=%.o)
GNL_OBJ := $(GNL_SRC:%.c=%.o)
PRINTF_OBJ := $(PRINTF_SRC:%.c=%.o)

CC = gcc
CCFLAGS = -Wextra -Wall -Werror

all: $(NAME)

$(NAME): $(OBJ) $(GNL_OBJ) $(PRINTF_OBJ)
    $(CC) $(CCFLAGS) $^ -Lmlx -lmlx -framework OpenGL -framework AppKit -o $(NAME)

%.o: %.c
	gcc $(CCFLAGS) -Imlx -Iincludes -c $< -o $@

clean:
	rm -f $(OBJ) $(GNL_OBJ) $(PRINTF_OBJ)

fclean: clean
	make clean -C mlx/
	rm -f $(NAME)

re : fclean all
```
{% endcode %}