---
description: 숫자를 정렬하게 만드는 프로젝트
---

# push\_swap

Push\_swap은 정렬 알고리즘과 최적화 방법에 대해 학습하는 프로젝트입니다. 이 프로젝트는 제한된 연산(push, swap, rotate)만을 사용하여 정수 Stack을 정렬하고, 필요한 동작(move)의 횟수를 최소화하는 것을 목표로 합니다.

시작 시점에는 모든 숫자가 함께 배치된 `stack a`라는 Stack 하나를 가지고 있어야 합니다. 아래에 설명된 연산들을 활용하여, 최종적으로 `stack a`에 있는 모든 숫자가 정렬되어야 합니다.

{% hint style="danger" %}
최종적으로 정렬된 리스트를 반환할 필요는 없으며, 리스트를 정렬하는 데 사용된 일련의 **연산 시퀀스**만 반환하면 됩니다.

필요하다면 리스트를 정규화(normalize)할 수 있습니다.
{% endhint %}

이를 위해 요소를 임시로 보낼 수 있는 두 번째 빈 Stack, 즉 `stack b`를 사용할 수 있습니다. 아래는 여러분이 사용할 수 있는(그리고 직접 코딩해야 하는) 연산들입니다.

* `sa (swap a)`: Stack a의 맨 위 2개 요소를 바꿉니다(Swap). 요소가 하나이거나 없으면 아무 작업도 하지 않습니다.
* `sb (swap b)` : Stack b의 맨 위 2개 요소를 바꿉니다(Swap). 요소가 하나이거나 없으면 아무 작업도 하지 않습니다.
* `ss` : sa와 sb를 동시에 수행합니다.
* `pa (push a)`: Stack b의 맨 위 요소를 꺼내 Stack a의 맨 위에 넣습니다. b가 비어 있으면 아무 작업도 하지 않습니다.
* `pb (push b)`: Stack a의 맨 위 요소를 꺼내 Stack b의 맨 위에 넣습니다. a가 비어 있으면 아무 작업도 하지 않습니다.
* `ra (rotate a)`: Stack a의 모든 요소를 한 칸 위로 올립니다(Shift). 첫 번째 요소는 마지막 요소가 됩니다.
* `rb (rotate b)` : Stack b의 모든 요소를 한 칸 위로 올립니다(Shift). 첫 번째 요소는 마지막 요소가 됩니다.
* `rr` : ra와 rb를 동시에 수행합니다.
* `rra` (reverse rotate a): Stack a의 모든 요소를 한 칸 아래로 내립니다(Shift). 마지막 요소는 첫 번째 요소가 됩니다.
* `rrb` (reverse rotate b): Stack b의 모든 요소를 한 칸 아래로 내립니다(Shift). 마지막 요소는 첫 번째 요소가 됩니다.
* `rrr` : rra와 rrb를 동시에 수행합니다.

### 예시

다음과 같이 **임의의 숫자 6개**가 첫 번째 Stack인 Stack A에 배치되어 있습니다. Stack B는 당연히 처음에 비어 있습니다. Stack B는 프로그램 전체에서 임시로 사용되지만, 최종적으로는 Stack A에 정렬된 리스트만 남고 Stack B는 다시 비워야 합니다.

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption><p>초기 Stack들</p></figcaption></figure>

Stack A에서 Stack B로 요소를 옮기려 한다고 가정해 보겠습니다. 이는 "PUSH" 연산을 통해 수행할 수 있습니다.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>PUSH 예시</p></figcaption></figure>

**Stack A의 첫 번째 요소가 Stack B의 첫 번째 요소가 됩니다.** Stack A의 두 번째 요소는 첫 번째 요소가 되는 식으로 이동한다는 점을 잊지 마십시오. 여기서는 Stack B가 비어 있었지만, 만약 숫자가 있었다면 Stack B의 첫 번째 요소는 두 번째 요소가 되는 식으로 밀려났을 것입니다. Stack B에서 Stack A로 요소를 보낼 때도 과정은 동일합니다.

위에서 수정된 리스트를 계속 사용해 보겠습니다. "SWAP" 연산을 사용하여 **Stack의 첫 두 숫자를 교환**할 수도 있습니다.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption><p>SWAP 예시</p></figcaption></figure>

여기서 영향을 받은 숫자는 Stack A의 첫 두 숫자뿐임을 확인할 수 있습니다. Stack B의 두 요소를 교환할 때도 논리는 동일합니다. 이 연산은 쉽습니다.

마지막으로 수행할 수 있는 연산 유형이 있습니다. 바로 **회전(Rotation)**입니다. 회전과 역회전, 두 가지 유형의 회전이 있습니다. 이번에는 Stack의 모든 숫자가 영향을 받습니다.

일반 회전은 모든 숫자를 위로 밀어 올리고, 역회전은 모든 숫자를 아래로 내립니다. 다음은 두 회전에 대한 개략적인 예시입니다.

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption><p>ROTATE 예시</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption><p>REVERSE ROTATE 예시</p></figcaption></figure>

이것으로 명령어 설명은 마칩니다. 물론 여러분은 모든 명령어를 조합하여 자신만의 알고리즘을 작성해야 합니다. 이 몇 가지 예시가 프로젝트를 더 잘 이해하고 코딩을 더 쉽게 하는 데 도움이 되기를 바랍니다. 이제 시작해 봅시다!