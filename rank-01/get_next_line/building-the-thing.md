# ▪️ 구현하기

이 함수에 대해서는, 함수를 구현하는 데 필요한 각 단계를 자세히 설명하는 것이 상당히 어렵기 때문에 단계별 구현 방식을 채택하지 않겠습니다.

대신, 이 기능을 구현하기 위해 수행해야 하는 작업에 대한 힌트를 드리겠습니다.

### LIBFT 함수

이 프로젝트를 위해, 여러분이 이미 알고 구현했던 LIBFT 함수들 중 일부를 사용했습니다.

정확히 언제 어디서 이 함수들을 사용해야 하는지 알려드리지는 않겠지만, 제가 사용한 함수들을 알려드리겠습니다. 이는 어디서부터 시작해야 할지에 대한 아이디어를 주거나 막힌 부분에서 해결책을 찾는 데 도움이 될 수 있습니다.

* FT\_STRCHR
* FT\_STRDUP
* FT\_STRLEN
* FT\_SUBSTR
* FT\_STRJOIN

### 추가 함수

이 5가지 함수 외에도, 저는 3가지 다른 함수를 사용했습니다:

* `char *get_next_line(int fd)`
* `char *_fill_line_buffer(int fd, char *left_c, char *buffer)`
* `char *_set_line(char *line_buffer)`

### 함수 설명

이 3가지 함수 각각은 프로젝트 내에서 고유한 용도를 가지고 있습니다. 저는 여기서 각 함수가 무엇을 하는지 설명해 드릴 것이며, 이는 여러분이 무엇을 해야 할지에 대한 아이디어를 제공할 수 있습니다. 또한, 제가 LIBFT 함수들을 어디에 사용했는지 더 잘 파악할 수 있을 것입니다.

<details>

<summary><code>char *get_next_line(int fd)</code></summary>

메인 함수인 `get_next_line`은 주로 File descriptor와 잘못될 수 있는 다양한 Memory allocation에 대한 몇 가지 검사를 수행합니다.

모든 검사가 완료되면, File descriptor에서 `\n` 또는 `\0` 문자를 찾을 때까지 읽기 위해 `_fill_line_buffer` 함수를 호출합니다.

`line` 변수가 채워지면, 그 이후에는 Buffer가 사용되지 않으므로 Memory leaks를 방지하기 위해 Buffer를 해제(free)합니다.

Buffer가 해제되면, `_set_line` 함수를 사용하여 라인을 설정하고 그 라인을 반환합니다. 이때 `_set_line`의 반환값을 Static variable에 저장하여 다음에 `get_next_line` 함수를 호출할 때 이전에 읽었을 수 있는 라인의 첫 문자들에 접근할 수 있도록 합니다.

예를 들어, 파일에 `1\n234\0`이 포함되어 있고, `BUFFER_SIZE`가 4라고 가정해 봅시다. 파일을 처음 읽을 때 `1\n23`을 읽게 됩니다. 이때 Static variable에 저장할 내용은 `23`이 됩니다. 왜냐하면 다음에 같은 File descriptor에 대해 이 함수를 호출할 때는 파일의 `4` 문자부터 읽기 시작해야 하기 때문입니다.

</details>

<details>

<summary><code>char *_fill_line_buffer(int fd, char *left_c, char *buffer)</code></summary>

이 함수는 `line` Buffer를 채웁니다.

이 함수는 `line` Buffer에서 `\n` 또는 `\0` 문자가 나올 때까지 각 반복마다 `BUFFER_SIZE`만큼의 문자를 읽습니다.

루프를 돌 때마다 `left_c` Buffer에 이미 데이터가 있는지 확인합니다. 만약 데이터가 있다면 새로 읽은 문자들을 그 뒤에 덧붙입니다(append). 데이터가 없다면, 읽은 Buffer의 내용을 `left_c` Buffer로 복제(duplicate)합니다.

`\n`이 발견되면, 루프를 중단하고 읽은 문자들을 덧붙인 후 `left_c` Buffer를 반환합니다.

</details>

<details>

<summary><code>char *_set_line(char *line_buffer)</code></summary>

이 함수는 매개변수로 `line` Buffer를 받으며, 라인의 끝 또는 파일의 끝을 의미하는 `\n` 또는 `\0` 문자를 찾을 때까지 Buffer를 읽습니다.

이 함수는 `line_buffer` 내부의 라인 끝에 `\0`을 설정하고, 라인의 끝에서부터 Buffer의 끝까지의 Substring을 반환합니다.

이 Substring은 `left_c`로 반환됩니다.

</details>

이것이 이 프로젝트의 주요 함수들입니다. 이 함수들이 제 프로젝트에서 각각 어떤 역할을 하는지에 대한 설명이 여러분 자신의 프로젝트를 진행하는 데 도움이 되기를 바랍니다.

다음 섹션에서는 이 3가지 함수에 대한 주석이 완벽히 달린 코드를 찾을 수 있습니다. 다른 함수들은 이미 여러분이 수정하고 이해했을 것이기 때문에 따로 설명하지 않겠습니다.