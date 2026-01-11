---
설명: >-
  프로젝트 구현 과정에서 혼동을 줄이기 위한 특정 개념에 대한 설명입니다.
---

# 🗡️ 그래픽 프로그래밍

초기에 웹에서 찾은 모든 용어들을 구별하는 데 큰 어려움을 겪었습니다. 이 새로운 챕터는 매우 방대합니다. 그래픽 프로그래밍이라는 이 완전히 새로운 챕터를 연습해 봅시다. (정말로 재미있으니 믿으셔도 됩니다. :-))

### Display VS Window VS Image (디스플레이 대 윈도우 대 이미지)

코딩을 시작할 때 가장 먼저 해야 할 일은 작업 공간을 초기화하는 것입니다. 이 세 가지 용어는 매우 비슷해 보이지만, 실제로는 매우 다릅니다.

#### **Display (디스플레이)**

Display는 시각적 정보를 보여주는 장치입니다. 그래픽 프로그래밍의 맥락에서 이는 그래픽 사용자 인터페이스(GUI)를 표시하는 물리적 장치를 의미합니다. 예를 들어, 컴퓨터를 사용할 때 Display는 모니터나 HDMI를 통해 컴퓨터에 연결된 TV일 수 있습니다.

#### **Window (윈도우)**&#x20;

Window는 **Display 상의 직사각형 영역을 나타내는** 그래픽 요소입니다. 이는 텍스트, 이미지 또는 비디오와 같은 콘텐츠를 표시하는 데 사용됩니다. 예를 들어, Window에는 텍스트 편집기, 웹 브라우저 또는 비디오 플레이어가 포함될 수 있습니다.

#### **Image (이미지)**

Image는 Display에 표시될 수 있는 **2차원 픽셀 배열**입니다. 이는 사진과 같은 정적 이미지이거나 비디오와 같은 동적 이미지일 수 있습니다. 그래픽 프로그래밍에서 Image는 종종 아이콘, 배경 또는 시각 효과와 같은 그래픽 요소를 표시하는 데 사용됩니다. Image는 단독으로 표시되거나 Window의 일부로 표시될 수 있습니다.

Display, Window, Image는 여러 가지 다른 크기(픽셀 단위로 정의됨)를 가질 수 있습니다. 어떤 크기가 수행하려는 작업과 필요한 것에 가장 적합한지는 여러분이 정의해야 합니다. 더 나은 시각화를 위해 작은 다이어그램을 삽입합니다:

[insert scheme]

<details>

<summary>C 언어 예시</summary>

그래픽 프로그래밍을 시작하기 전에 항상 Display와 하나 이상의 Window 및 Image를 초기화하는 것을 고려해야 합니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
int	main(void)
{
	void	*mlx; //디스플레이
	void	*mlx_win; //윈도우
	t_data	image; //이미지

	mlx = mlx_init(); //디스플레이 초기화
	mlx_win = mlx_new_window(mlx, 1920, 1080, "Hello world!"); //윈도우 초기화
	image.img = mlx_new_image(mlx, 1920, 1080); //이미지 초기화
}
```
{% endcode %}

</details>

### 프로그램과 상호작용하기 (Hook을 사용하여)

그래픽 프로젝트를 진행할 때, 작업 중인 Window와 상호 작용할 수 있습니다. 예를 들어 Window를 닫거나, 확대/축소하거나, 다른 많은 작업을 수행할 수 있습니다. 이는 "hook"이라는 기능을 통해 수행될 수 있습니다.

컴퓨터 과학에서 **Hook**은 함수 또는 시스템 호출의 동작을 가로채고 수정하는 방법입니다. 이는 종종 추가 기능을 추가하거나 프로그램 또는 운영 체제의 동작을 사용자 정의하는 데 사용됩니다.

<details>

<summary>Hook의 간단한 예시</summary>

키보드에서 특정 키를 누를 때마다 화면에 메시지를 표시하는 프로그램이 있다고 상상해 보세요. 키가 눌릴 때마다 메시지 색상을 변경하거나 소리를 재생하는 등 이 프로그램에 몇 가지 추가 동작을 추가하고 싶을 수 있습니다.

이를 위해 키 누름 이벤트를 처리하는 함수를 "Hook"할 수 있습니다. 키가 눌리면 Hook 함수가 이벤트를 가로채서 메시지 색상 변경 또는 소리 재생과 같이 원하는 추가 동작을 수행합니다. 그런 다음, 이벤트를 원래 함수로 전달하여 메시지가 평소와 같이 표시되도록 합니다.

다음은 작동 방식을 보여주는 의사 코드입니다:

{% code overflow="wrap" lineNumbers="true" %}
```c
//키 누름 이벤트를 처리하는 원래 함수
void OnKeyPress(Key key)
{
    // 메시지를 표시합니다
    DisplayMessage("You pressed the key: " + key);
}

// 키 누름 이벤트를 가로채는 Hook 함수
void Hooked_OnKeyPress(Key key)
{
    // 추가 동작을 수행합니다
    ChangeMessageColor(Color.Red);
    PlaySound("beep.wav");

    // 이벤트를 원래 함수로 전달합니다
    OnKeyPress(key);
}

// OnKeyPress 함수를 Hook 합니다
void HookOnKeyPress()
{
    // OnKeyPress 함수를 Hook 함수로 대체합니다
    OnKeyPress = Hooked_OnKeyPress;
}

// 메인 프로그램 루프
void Main()
{
    // OnKeyPress 함수를 Hook 합니다
    HookOnKeyPress();

    // 키 누름 이벤트를 기다립니다
    while (true)
    {
        Key key = WaitForKeyPress();
        OnKeyPress(key);
    }
}

```
{% endcode %}

</details>

이 개념은 프로젝트의 나머지 부분에서 정말 유용하게 사용될 것입니다.