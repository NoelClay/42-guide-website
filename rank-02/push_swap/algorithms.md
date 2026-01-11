# ▪️ 알고리즘 (Algorithms)

코드와 관련하여, 이 프로젝트에서는 새로운 것을 배우지는 않습니다. 단지 연결 리스트(chained lists) 또는 배열(arrays) 관리와 구조체(structures) 사용을 숙달하는 것 외에는 말입니다. 하지만 push\_swap에서 우리가 발견하게 되는 가장 중요한 개념은 바로 Algorithm입니다.

### Algorithm이란 무엇입니까?

Algorithm은 특정 문제를 해결하거나 작업을 완료하기 위한 일련의 단계입니다.

마치 처음부터 케이크를 만드는 방법을 설명하는 레시피와 같다고 생각하시면 됩니다. 각 단계는 명확하게 정의되어 있으며 원하는 결과를 얻기 위해 특정 순서대로 따라야 합니다. 또 다른 예로는 루빅스 큐브를 푸는 것이 있습니다. 특정 모양을 보게 되면, 특정한 지침을 실행해야 합니다.

**Algorithm은 간단한 산술 연산부터 복잡한 머신러닝 모델에 이르기까지 광범위한 문제에 적용될 수 있습니다.**

Algorithm을 학습하는 것은, 특히 push\_swap의 맥락에서 볼 때, 여러 가지 이유로 유용합니다.

*   **문제 해결 능력**: Algorithm은 문제를 이해하고 해결책을 고안한 다음 이를 구현하도록 요구하기 때문에 문제 해결 능력을 개발하는 데 도움을 줍니다.
*   **자료 구조(Data Structures)에 대한 이해**: "push\_swap" 문제는 데이터를 정렬하는 것을 포함하므로, Algorithm을 이해하면 Stack과 Array와 같은 다양한 자료 구조와 이를 조작하는 방법을 이해하는 데 도움이 됩니다.
*   **효율성 향상**: Algorithm을 이해하면 문제를 해결하는 데 필요한 연산의 수를 줄여 코드의 효율성을 높이는 데 도움이 됩니다.
*   **기술 면접 대비**: Algorithm에 대한 지식은 지원자의 문제 해결 능력과 기본적인 컴퓨터 과학 개념에 대한 이해를 제공하기 때문에 기술 면접에서 자주 테스트됩니다.

하나의 문제는 항상 여러 해결책을 가질 수 있습니다. 어떤 해결책은 다른 해결책보다 더 효율적입니다. 이는 즉, 하나의 Algorithm이 다른 Algorithm보다 더 효율적이고 최적화될 수 있음을 의미합니다. 이를 Algorithm Complexity(알고리즘 복잡도)라고 합니다.

### Algorithm Complexity (알고리즘 복잡도)

Algorithm Complexity는 Algorithm이 문제를 해결하는 데 필요한 자원(예: 시간 또는 메모리)의 양을 나타냅니다. 이는 서로 다른 Algorithm들의 효율성을 측정하고 비교하는 방법을 제공합니다.

Algorithm Complexity에는 두 가지 주요 유형이 있습니다.

1.  **Time complexity**: Time complexity는 Algorithm이 문제를 해결하는 데 걸리는 시간을 측정합니다.
2.  **Space complexity**: Space complexity는 Algorithm이 문제를 해결하는 데 걸리는 메모리 양을 측정합니다.

#### Algorithm Complexity의 측정

Complexity는 일반적으로 Big O Notation을 사용하여 표현되는데, 이는 Algorithm이 요구하는 자원의 상한선을 제공합니다. 따라서 가장 고전적인 접근 방식은 최악의 경우(worst-case) 계산 시간을 산출하는 것입니다. 예를 들어 보겠습니다.

입력 크기가 10인 문제를 해결하는 데 100밀리초가 걸리는 Algorithm이 있다고 가정해 보겠습니다. 입력 크기를 두 배로 늘리면 최대 200밀리초가 걸릴 수 있습니다. 입력 크기가 30이면 최대 300밀리초가 걸릴 수 있습니다. 이런 식으로 진행됩니다. 이는 해당 Algorithm의 Time complexity가 O(n)임을 의미하며, 여기서 n은 입력의 크기입니다.

다음은 여러 단계의 Complexity/Big O Notation을 서로 비교하는 다이어그램입니다.

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption><p>Algorithm 비교</p></figcaption></figure>

1.  O(1)은 상수 시간 복잡도(constant time complexity)를 나타내며, Algorithm에 필요한 시간이 입력 크기에 의존하지 않음을 의미합니다. 예를 들어, 인덱스를 사용하여 Array의 요소에 접근하는 것은 O(1)입니다.
2.  O(n)은 선형 시간 복잡도(linear time complexity)를 나타내며, Algorithm에 필요한 시간이 입력 크기에 선형적으로 비례하여 증가함을 의미합니다. 예를 들어, 단순한 선형 검색(linear search) Algorithm은 O(n)입니다.
3.  O(log n)은 로그 시간 복잡도(logarithmic time complexity)를 나타내며, Algorithm에 필요한 시간이 입력 크기에 로그적으로 비례하여 증가함을 의미합니다. 예를 들어, 이진 검색(binary search) Algorithm은 O(log n)입니다.
4.  O(n^2)은 2차 시간 복잡도(quadratic time complexity)를 나타내며, Algorithm에 필요한 시간이 입력 크기에 제곱으로 비례하여 증가함을 의미합니다. 예를 들어, 단순한 Bubble Sort Algorithm은 O(n^2)입니다.

Big O Notation은 Algorithm의 증가율에 대한 상한선(upper bound)을 제공하며, Algorithm이 요구하는 자원의 정확한 측정값은 아니라는 점에 유의해야 합니다. Algorithm이 실제로 요구하는 자원은 Big O Notation이 제공하는 추정치와 다를 수 있습니다.

### 알려진 Sorting Algorithm

push\_swap 프로젝트를 완수하기 위해서는, 허용된 명령어와 동작하며 최소한의 동작(moves)으로 정렬을 수행할 수 있는 Sorting Algorithm을 선택해야 합니다.

아래는 Sorting Algorithm의 예시와 Time complexity를 세 가지 다른 방법으로, Space complexity를 한 가지 방법으로 나타낸 표입니다.

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption><p>Sorting Algorithm의 Complexity. 출처: Big-O Cheat Sheet, 2016.</p></figcaption></figure>

이제 여러분이 어떤 Algorithm을 사용할지 선택할 차례입니다! 물론 가능하다면 여러분만의 Algorithm을 직접 만들어 볼 수도 있습니다 :-)

push\_swap 프로젝트에 행운이 있기를 바랍니다 ;)