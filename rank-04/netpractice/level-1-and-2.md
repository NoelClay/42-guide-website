# 레벨 1 & 2

각 레벨을 차근차근 진행하면서, 각 문제를 해결하기 위해 스스로에게 올바른 질문을 던져보도록 하겠습니다.

추신: 답을 즉시 확인하지 마십시오. 그렇지 않으면 아무것도 배우지 못하고 평가(evaluation) 중에 어려움을 겪을 수 있습니다.

## 0.1 IP 주소를 찾는 방법 _(하나의 Mask와 하나의 IP를 사용하여)_

다른 장비(Interface 1)의 IP 주소와 장비 2(Interface 2)의 Mask를 사용하여 Interface 2의 IP 주소를 찾아보십시오.

*   IP 주소와 Subnet mask를 이진수(binary)로 변환합니다.

Interface 1 IP address: 192.168.25.222 -> 11000000.10101000.00011001.11011110 \
Interface 2 subnet mask: 255.255.255.224 -> 11111111.11111111.11111111.11100000

*   IP 주소의 각 비트와 Subnet mask의 각 비트 사이에 Bitwise "AND" 연산을 수행합니다.

11000000.10101000.00011001.11011110 \
11111111.11111111.11111111.11100000 \
\
\= 11000000.10101000.00011001.11000000

*   이진수 결과를 다시 십진수(decimal) 표기법으로 변환합니다.

\--> 따라서, 제공된 Subnet mask가 이 특정 상황의 네트워크 구성에 적용될 수 있다고 가정하면, Interface 2의 IP 주소는 192.168.25.192가 될 수 있습니다.

이제 주소 범위(address range)에 관해 설명하자면, Subnet mask가 255.255.255.224 (또는 /27)인 경우, [**사용 가능한 주소 범위는 30개의 주소입니다**](https://42-cursus.gitbook.io/guide/rank-04/netpractice/tcp-ic-and-masks#masks-subnet-masks). 이 경우, 주소 범위는 192.168.25.192부터 192.168.25.223까지가 됩니다.

이 범위 내의 모든 IP 주소는 네트워크상의 다른 Interface나 장치에서 이미 사용되고 있지 않는 한, Interface 2에 대해 유효하다고 간주된다는 점을 유의하는 것이 중요합니다.

따라서 만약 응답으로 192.168.25.194를 표시했다면, 이는 주어진 Subnet mask로 사용 가능한 주소 범위 내에 있으므로, Interface 2에 대한 유효한 IP 주소가 될 수도 있습니다.

## 0.2 Subnet Mask를 찾는 방법 (하나의 IP와 하나의 Mask를 사용하여)

Interface 1의 IP 주소와 Interface 2의 IP 주소를 사용하여 Interface 2의 Subnet mask를 찾으려면 다음 단계를 따를 수 있습니다.

*   두 IP 주소를 모두 이진수(binary)로 변환합니다.

Interface 1 IP address: 192.168.25.222 -> 11000000.10101000.00011001.11011110 \
Interface 2 IP address: 192.168.25.192 -> 11000000.10101000.00011001.11000000

*   Interface 1의 IP 주소와 Interface 2의 IP 주소의 비트(bit)를 비교합니다.

IF1: 11000000.10101000.00011001.11011110 \
IF2: 11000000.10101000.00011001.11000000

최상위 비트(most significant bit, 왼쪽에서 오른쪽으로)부터 시작하여 두 IP 주소의 각 대응하는 비트를 비교합니다. 두 IP 주소 간에 비트가 달라지는 첫 번째 지점을 찾습니다.

이 지점부터는 Subnet mask의 모든 후속 비트(bit)가 0으로 설정되어야 합니다.

\= 11111111.11111111.11111111.00000000

이 경우, 연속된 1 비트의 가장 긴 시퀀스는 "24 bits"입니다.

*   이 비트 시퀀스를 십진수 표기법으로 변환합니다.

Interface 2 Subnet mask: 255.255.255.0

따라서, 제공된 IP 주소들이 이 특정 상황에서 유효한 네트워크 구성에 해당한다고 가정하면, Interface 2의 Subnet mask는 255.255.255.0이 됩니다.

이제 방금 배운 내용을 실제로 적용해 보겠습니다 :)

## 레벨 1 : 단순한 경우 (Simple case)

목표는 두 쌍의 컴퓨터를 각각 연결하는 것입니다.

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption><p>NetPractice : Level 1</p></figcaption></figure>

여기에는 각각 두 대의 컴퓨터로 구성된 두 개의 개별 네트워크가 있습니다. 제가 말씀드린 첫 번째 방법을 사용하여 그들의 IP 주소를 찾아보십시오.

Interface A1의 IP 주소를 찾으려면, A1의 Mask와 B1의 IP 주소를 사용해야 합니다.

## 레벨 2 : Mask가 약간 수정된 경우 (Masks are a bit modified)

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption><p>Level 1 Example</p></figcaption></figure>

여기에는 각각 두 대의 컴퓨터로 구성된 두 개의 개별 네트워크가 있습니다.