# ▪️ so\_long 이해하기

### 목표

<details>

<summary>프로젝트별 지침</summary>

간단한 2D 게임(Top-down 또는 Platformer)을 구축해야 합니다.

* 게임의 목표는 맵에 존재하는 모든 오브젝트를 수집하여 출구를 잠금 해제하는 것입니다.
* W, A, S, D 키를 사용하여 움직일 수 있어야 합니다.
* 플레이어는 네 방향(위, 아래, 오른쪽, 왼쪽) 모두로 움직일 수 있습니다.
* 플레이어는 벽을 통과할 수 없습니다.
* 셸에서 움직일 때마다 총 이동 횟수가 표시되어야 합니다.
* ESC 키를 누르면 창이 닫히고 프로그램이 올바르게 종료되어야 합니다.
* 창의 닫기 버튼(빨간색 엑스 표시)을 클릭하면 창이 닫히고 프로그램이 올바르게 종료되어야 합니다.
* MiniLibX Images를 사용하는 것은 필수입니다.

</details>

요약하자면, Top-down 방식 또는 측면 시점의 간단한 2D 게임을 만들어야 합니다.

프로젝트 주제 자체는 해야 할 작업에 대해 명확하게 설명하고 있습니다. 다음 페이지에서 게임 제작을 시작하기 전에 알아야 할 몇 가지 핵심 개념을 설명하는 시간을 갖겠습니다.

### 시작하기 전에

이 프로젝트 작업을 시작하고 제가 작성한 내용을 읽기 전에, MiniLibX 문서를 [여기](https://harm-smits.github.io/42docs/libs/minilibx)에서 시간을 내어 읽어보시기를 권장합니다. 이는 "공식" 문서는 아니지만, 내용이 더 잘 설명되어 있습니다.

이 Gitbook에는 제가 사용했던 몇 가지 헬퍼 함수(Helper functions)와 이 프로젝트에 유용할 수 있는 몇 가지 후크(Hooks)를 정리해 둔 [MiniLibX](broken-reference) 섹션도 있습니다.

또한, 만들고 싶은 게임 유형을 선택해야 합니다. Top-down 시점인지, 아니면 측면에서 보는 방식(예: Platformer)인지 결정해야 합니다.

{% hint style="info" %}
2D Top-down 게임은 Top-down 관점에서 플레이되는 비디오 게임 유형입니다. 즉, 카메라가 게임 세계 위에 위치하며 플레이어는 위에서 내려다보는 관점에서 게임을 보게 됩니다. 2D Top-down 게임에서 게임 세계는 일반적으로 2차원으로 표현됩니다. 이는 게임 세계가 완전히 3차원 공간이 아닌 평평한 평면으로 표시됨을 의미합니다.

2D Top-down 게임에서 플레이어는 일반적으로 단일 Sprite 또는 2D 그래픽으로 표현되는 캐릭터를 제어하며, 키보드의 화살표 키나 WASD 키, 또는 게임 패드나 조이스틱을 사용하여 게임 세계를 돌아다닙니다. 플레이어는 일반적으로 캐릭터를 움직이고, 아이템을 수집하고, 퍼즐을 풀고, 적을 물리치는 방식으로 게임 세계와 상호 작용합니다.

2D Top-down 게임은 비디오 게임 초창기에 인기가 있었기 때문에 향수나 레트로 스타일을 불러일으키는 데 자주 사용됩니다. 또한 턴제 전략 게임 또는 많은 탐험과 퍼즐 풀이가 필요한 게임과 같은 특정 유형의 게임 플레이에 적합합니다.
{% endhint %}

{% hint style="info" %}
2D Platformer 게임은 달리고, 점프하고, 장애물과 적을 피하면서 일련의 레벨 또는 환경을 캐릭터가 탐색하는 방식의 비디오 게임 유형입니다. 2D Platformer 게임에서 게임 세계는 일반적으로 2차원으로 표현됩니다. 이는 게임 세계가 완전히 3차원 공간이 아닌 평평한 평면으로 표시되며 측면에서 보여짐을 의미합니다(Mario가 좋은 예입니다).

2D Platformer 게임에서 플레이어는 일반적으로 단일 Sprite 또는 2D 그래픽으로 표현되는 캐릭터를 제어하며, 키보드의 화살표 키나 WASD 키, 또는 게임 패드나 조이스틱을 사용하여 게임 세계를 돌아다닙니다. 플레이어는 일반적으로 캐릭터를 움직이고, 아이템을 수집하고, 퍼즐을 풀고, 적을 물리치는 방식으로 게임 세계와 상호 작용합니다.

2D Platformer 게임은 플레이어가 진행해야 하는 일련의 레벨을 포함하는 경우가 많으며, 플레이어가 진전함에 따라 각 레벨의 난이도는 점차 높아집니다. 플레이어는 레벨을 더 쉽게 탐색할 수 있도록 도와주는 Power-up이나 특수 능력을 얻을 수도 있습니다.

2D Platformer 게임은 인기 있는 비디오 게임 장르이며, 비디오 게임 초창기부터 존재해 왔습니다. 이 장르는 종종 빠른 속도의 액션과 도전적인 게임 플레이로 알려져 있으며, 수년 동안 비디오 게임 산업의 주요 장르로 자리매김해 왔습니다.
{% endhint %}

### 2D 게임 예시

아이디어가 필요할 경우 영감을 얻을 수 있는 잘 알려진 2D 게임 세 가지 예시를 소개합니다.

<figure><img src="../../.gitbook/assets/hotline_miami.avif" alt=""><figcaption><p>Hotline Miami</p></figcaption></figure>

{% hint style="info" %}
Hotline Miami에 대해 더 자세히 알아보시려면 [여기](https://en.wikipedia.org/wiki/Hotline_Miami)를 클릭하십시오.
{% endhint %}

<figure><img src="../../.gitbook/assets/super_mario_bros.jpg" alt=""><figcaption><p>Super Mario Bros.</p></figcaption></figure>

{% hint style="info" %}
여러분 모두 Super Mario Bros.가 무엇인지 알고 계시겠지만, 정보는 [여기](https://en.wikipedia.org/wiki/Super_Mario_Bros.)에서 찾을 수 있으며, [여기](https://supermario-game.com/)에서 온라인으로 플레이할 수도 있습니다.
{% endhint %}

<figure><img src="../../.gitbook/assets/terraria.jpg" alt=""><figcaption><p>Terraria</p></figcaption></figure>

{% hint style="info" %}
Terraria에 대한 더 자세한 정보는 [여기](https://en.wikipedia.org/wiki/Terraria)에서 찾으실 수 있습니다.
{% endhint %}
