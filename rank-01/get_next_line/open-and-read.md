# ▪️ open() 및 read()

이 프로젝트를 시작하려면 먼저 텍스트 파일을 열 수 있어야 합니다. 파일을 열면 내용을 읽을 수 있습니다. 이는 `open()` 함수와 `read()` 함수를 사용하여 수행할 수 있습니다.

{% hint style="danger" %}
PS: file descriptor (fd)가 무엇인지 모른다면, 계속 진행하기 전에 다음 페이지를 확인해 보시는 것이 좋습니다.
{% endhint %}

{% content-ref url="../../useful-tools/file-descriptors-fd.md" %}
[file descriptor (fd) 에 대한 설명](../../useful-tools/file-descriptors-fd.md)
{% endcontent-ref %}

##

## open ()

이 함수가 작동하려면 먼저 다음 라이브러리를 포함해야 합니다.

```c
#include <fcntl.h>
```

이 함수는 파일을 열고 접근할 수 있도록 해줍니다. 함수 원형은 다음과 같습니다.

```c
int open (const char* path, int flags [, int mode ]);
```

#### Path

이 인자는 열거나 생성하려는 파일의 이름을 나타냅니다.

또한 파일의 위치를 가리킵니다. 파일과 같은 디렉토리에 있지 않은 경우, "/"로 시작하는 absolute path를 제공할 수 있습니다.

#### Flags

함수에 어떤 종류의 접근 권한을 원하는지 알려주어야 합니다. 이는 flags를 사용하여 수행됩니다. 각 flag의 정보와 목록은 다음과 같습니다.

*   **O\_RDONLY**: 파일을 읽기 전용 모드로 엽니다.
*   **O\_WRONLY**: 파일을 쓰기 전용 모드로 엽니다.
*   **O\_RDWR**: 파일을 읽기 및 쓰기 모드로 엽니다.
*   **O\_CREAT**: 지정된 path 또는 디렉토리에 파일이 존재하지 않는 경우 파일을 생성하도록 적용되는 flag입니다.
*   **O\_EXCL**: 디렉토리나 위치에 파일이 이미 존재하는 경우 파일 생성을 방지합니다.

#### Return Value

`open()` 함수의 반환 값은 file descriptor이며, 이는 프로세스의 열린 file descriptor 테이블 항목을 가리키는 인덱스인 작고 음이 아닌 정수입니다. 만약 오류가 발생하면, 함수는 실패를 의미하는 -1을 반환합니다.

<details>

<summary>예시</summary>

파일을 열 수 있도록 `text.txt` 파일이 존재해야 합니다.

```c
int main()
{
    int fd;
    fd = open("text.txt", O_RDONLY);
}
```

</details>

현재로서는 아무것도 표시되지 않습니다. 왜냐하면 이 함수는 단순히 파일을 여는 데만 사용되기 때문입니다. 함수를 유용하게 사용하려면 추가 함수를 사용해야 합니다. 예를 들어, 지금부터 함께 살펴볼 `read()` 함수가 있습니다.

## read ()

함수의 원형은 다음과 같습니다.

```c
ssize_t read(int fildes, void *buf, size_t nbyte);
```

이 함수는 descriptor `fildes`가 참조하는 객체로부터 `nbyte` 크기의 데이터를 `buf`가 가리키는 buffer로 읽어들이려고 시도합니다. `read()` 함수는 `fildes`와 연결된 Pointer가 지정하는 위치에서 시작합니다. 작업이 완료되면, Pointer는 실제로 읽어 들인 바이트 수(`nbyte`)만큼 증가합니다.

이 내용은 아래 영상에 잘 설명되어 있으므로, 다시 자세히 다루지는 않겠습니다.

{% embed url="https://www.youtube.com/watch?t=2s&v=-Mt2FdJjVno" %}
프랑스어 영상이지만, 영어 자막이 제공됩니다!
{% endembed %}

이제 이 두 함수가 어떻게 작동하는지 알았으니, 다음 단계인 [static variables 이해하기](static-variables.md)로 넘어가 봅시다. 이 개념은 다른 프로젝트에서도 많이 사용될 것입니다! 다음 문서에서 뵙겠습니다. :smile:
