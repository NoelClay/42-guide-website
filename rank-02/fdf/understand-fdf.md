# 🗡️ FdF 이해하기

FdF는 새로운 지식을 습득하고 많은 새로운 도구, 특히 42의 minilibx를 숙달해야 하는 그래픽 프로젝트입니다. 하지만 주제 자체가 이해하기 복잡할 수도 있습니다... 어떻게 제대로 수행해야 할까요? 이 페이지에서 이에 대해 자세히 알아보겠습니다.

### 3D에서 2D로

프로젝트 주제에서 우리는 지도 예시를 제공받습니다. 이 지도는 공간 내의 좌표 (x, y, z)에 해당합니다. 하지만 우리 컴퓨터의 화면은 두 가지 차원(x, y)만을 가진 표면입니다. 따라서 3D 포인트를 2D 평면에 투영해야 합니다.

제가 말씀드리는 바를 더 잘 시각화할 수 있도록 다음 그림을 참조하십시오.

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>3D 큐브를 2D 평면에 투영한 모습</p></figcaption></figure>

위에는 4가지 다른 투영 예시가 있습니다. 이 프로젝트에서는 "**isometric projection** (등축 투영)"만을 수행하도록 요청합니다.

좀 더 구체적인 예시를 살펴보고 처음부터 시작해 보겠습니다.

[우리가 해야 할 일을 더 잘 이해하기 위한 FdF 계획 삽입 - 3개의 축을 가진 3D 큐브와 화면을 나타내는 2개의 축을 가진 2D 큐브]

### 점들을 올바른 위치에 배치하기

8개의 점으로 이루어진 지도가 있다고 가정해 보겠습니다.

<mark style="color:blue;">(열 = 0, 1, 2, 3 = x)</mark>\
&#x20;          1  2  3  4 <mark style="color:blue;">(행 = 0 = y)</mark>\
&#x20;          5  6  7  8 <mark style="color:blue;">(행 = 1 = y)</mark>

이를 통해 8개의 점을 정의할 수 있습니다. 예를 들어:

* (x,y,z) = (0,0,1)    |    (x,y,z) = (1,0,2)    |    (x,y,z) = (1,1,6)     |   ... 등

하지만 컴퓨터 화면은 두 가지 좌표(x와 y)만을 가집니다. 컴퓨터에서 3D를 볼 수 없습니다. 이 3D 효과를 만들기 위해 특정 방식으로 점을 배치해야 합니다.

다행히도 이는 수학 공식으로 수행할 수 있습니다. 1도 투영(one degree of projection)의 경우 다음을 사용합니다.

```
destination.x = source.x + cos(angle) * source.z
destination.y = source.y + sin(angle) * source.z
```

데이터에서 **isometric projection**을 생성하도록 요구했기 때문에, 다른 공식을 사용해야 합니다. Isometric Projection은 물체의 세 가지 주요 모서리(물체의 세 가지 차원에 해당)가 120°의 동일한 각도를 형성하는 원근 표현입니다. 제가 사용한 공식은 다음과 같습니다.

$$
destination.x = source.x * cos(a) + source.y * cos(a + 2) + source.z * cos(a - 2)
$$

$$
destination.y = source.x * sin(a) + source.y * sin(a + 2) + source.z * sin(a - 2)
$$

동일한 결과를 얻기 위한 다른 공식들도 있습니다. 차이점을 확인할 수 있도록 직접 테스트해 보시는 것을 권장합니다.

### 점들을 연결하기

위의 이론을 이해하고 화면에 모든 점을 배치했다면, 이제 이 점들을 연결해야 합니다.

이를 위해 두 점을 서로 연결하는 알고리즘을 코딩해야 합니다. 이미 개발된 여러 유형의 알고리즘이 있으며, 이를 참고하여 영감을 얻을 수 있습니다. 몇 가지 예시는 다음과 같습니다.

* **DDA Line Algorithm**: [[여기 문서]](https://www.thecrazyprogrammer.com/2017/01/dda-line-drawing-algorithm-c-c.html)
* **Linear Interpolation:** [[여기 문서]](https://en.wikipedia.org/wiki/Linear_interpolation)
* **Bresenham:** [[여기 문서]](https://en.wikipedia.org/wiki/Bresenham's_line_algorithm)

개인적으로는 DDA가 가장 단순하고 동일한 결과를 달성하기 위해 덜 복잡했기 때문에 DDA를 사용했습니다.

### 요약

* [ ] 먼저 모든 3차원 점 (x, y, z)을 2차원 점 (x, y)으로 변환해야 합니다.
* [ ] 변환이 완료되면, 화면에 점을 배치합니다 (점 하나 = 픽셀 하나).
* [ ] 점들을 서로 연결하고 연결하는 모든 픽셀을 그리는 알고리즘을 코딩합니다.

이는 프로젝트를 위한 완전한 체크리스트는 아닙니다. 이후 최종 체크리스트에서 찾을 수 있는 몇 가지 다른 사항에도 주의를 기울여야 합니다. 이 요약은 투영의 필수적인 핵심 사항을 이해하는 데 도움을 주기 위한 것입니다.