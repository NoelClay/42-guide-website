---
description: 지금 무엇을 하고 있는지 이해하기 위한 몇 가지 정의
---

# 📠 Virtual Machine이란 무엇인가요?

간단히 말해, **Virtual Machine (VM)**은 여러분의 머신 안에 있는 또 하나의 머신입니다.

**Virtualization**은 마치 여러 시스템이 각각 분리된 물리적 머신에서 실행되는 것처럼, 동일한 물리적 머신에서 여러 시스템을 실행하는 기술입니다.

이 말이 무슨 뜻인지 더 잘 이해하고 Virtual Machine이 무엇에 사용되는지 더 잘 시각화하기 위해 기본으로 돌아가 보겠습니다. 우리는 이 프로젝트에서 만들어야 하는 것이 서버 머신이기 때문에, 컴퓨터 서버를 예시로 들어보겠습니다.

## 컴퓨터 서버의 구성 요소

물리적 서버에는 마더보드, CPU (프로세서), 정보를 일시적으로 저장하는 RAM, 정보를 장기간 저장하는 하드 디스크, 네트워크 카드 및 기타 여러 구성 요소와 같은 다수의 하드웨어 요소가 있습니다. 그 위에는 Operating System이 설치되며, 이 OS는 여러 애플리케이션을 포함할 수 있습니다.

Virtual Machine은 자신이 실행되는 머신의 하드웨어(즉, CPU 및 메모리)를 사용하지만, 그 내부의 모든 것은 다릅니다. Virtual Machine은 다른 Operating System과 많은 종류의 애플리케이션을 가질 수 있습니다.

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

위 다이어그램에서 제가 "Virtualization Layer(가상화 계층)"를 추가한 것을 볼 수 있습니다. 이 단계는 Hypervisor에 의해 수행됩니다.

## Hypervisor

**Hypervisor**는 사용자가 하나 이상의 Virtual Machine을 동시에 생성하고 실행할 수 있도록 하는 소프트웨어입니다. Hypervisor는 Virtual Machine Monitor (VMM)라고도 알려져 있으며, 호스트 머신의 리소스를 제어하고 각 VM에 필요한 리소스(메모리, CPU 등)를 할당하여, 이 VM들이 서로 간섭하지 않도록 보장합니다.

### VirtualBox

42에서는 **"VirtualBox"** (또는 VirtualBox가 작동하지 않을 경우 UTM)를 사용하는 것을 권장합니다. VirtualBox는 42 학교에서 Mac을 사용하고 있다면 여러분의 Mac에서 찾을 수 있는 오픈 소스 소프트웨어입니다. 그렇지 않다면 [설치할 수 있습니다](https://www.virtualbox.org/manual/ch02.html). VirtualBox는 Hypervisor 역할을 수행하며, 사용자가 다른 OS(Operating System)를 실행할 수 있는 VM (Virtual Machine)을 생성합니다.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Operating System

모든 머신은 **Operating System (OS)** 위에서 실행되는데, OS는 머신을 실행하고 제어할 수 있게 해주는 프로그램 세트입니다. 다시 말해, OS는 사용자, 프로그램 및 컴퓨터 구성 요소 사이의 인터페이스입니다. 예를 들어, Apple 머신은 주로 macOS에서 실행됩니다. 다른 머신은 Windows 또는 Linux와 같은 다른 두 가지 알려진 Operating System에서 실행될 수 있습니다.

여러분의 Virtual Machine 역시 Operating System을 사용해야 합니다. 여러분은 Debian과 CentOS 중에서 선택할 수 있습니다. 이 두 가지의 차이점을 살펴보겠습니다.

{% hint style="danger" %}
이 가이드를 작성한 이후 주제가 변경되었을 수 있으므로, 프로젝트를 검증하기 전에 주제를 정확히 읽어보시기 바랍니다.

정확히 어떤 차이점이 있는지 확인한 후 이 가이드를 업데이트하겠습니다.
{% endhint %}

### Debian VS CentOS

`Debian`은 최초의 Linux 배포판 중 하나이며 1993년부터 사용 가능했습니다. Debian은 더 높은 수준의 제어 및 구성을 위한 커스터마이징을 제공합니다.

2004년에 출시된 `CentOS`는 IT 업계에서 대규모로 흔히 사용됩니다. CentOS는 엔터프라이즈급 보안을 보장하여 사용자에게 안전합니다.

*   Debian은 새 버전이 출시될 때 CentOS보다 업데이트하기 훨씬 쉽습니다.
*   Debian은 더 사용자 친화적이며 많은 라이브러리, 파일 시스템 및 아키텍처를 지원합니다. 또한 커스터마이징을 위한 더 많은 옵션을 제공합니다.
*   만약 대규모 비즈니스라면, CentOS는 더 많은 엔터프라이즈 기능과 엔터프라이즈 소프트웨어에 대한 탁월한 지원을 제공합니다.

만약 초보자라면, 42는 Debian을 사용하는 것을 권장합니다.

{% hint style="info" %}
저는 Debian을 사용하여 이 GitBook을 계속 진행하겠습니다.
{% endhint %}

## Virtual Machine의 목적은 무엇인가요?

가장 명확한 첫 번째 요점은, 또 다른 컴퓨터를 구입하는 것보다 Virtual Machine을 설치하는 것이 **더 편리하고 저렴하다는** 것입니다. 게다가, VM의 **유지보수는 서버(또는 다른 물리적 머신)의 유지보수에 비해 매우 낮습니다**. 그리고 이 모든 것 외에도 **많은 공간을 절약할 수 있습니다**. 집에 X개의 머신을 보관하는 대신, 모두 같은 머신 위에 존재하기 때문입니다.

물리적 서버가 충돌하면, 서버에 포함된 데이터를 복구하는 것이 복잡할 수 있습니다. **VM의 소프트웨어적 측면은 데이터 백업을 단순화합니다.** VM이 실행되는 동안, VM과 해당 데이터의 snapshot 덕분에 백업을 얻는 것이 가능합니다. 사고가 발생하면, 이 snapshot을 통해 VM을 이전 상태로 복원할 수 있습니다.

Virtualization은 데이터를 분할하고 서로 다른 서버에 서비스를 격리함으로써 **데이터의 보안을 높일 수 있게** 해줍니다. 각 VM은 호스트 시스템을 포함하여 다른 VM과 격리되어 있습니다. 이는 맬웨어 침입 시 전파 위험을 제한합니다. 게다가, **한 Virtual Machine이 충돌해도 다른 VM에는 영향을 미치지 않습니다**.

하지만 단순히 **Operating System을 테스트**하거나 다른 것들을 위한 **테스트 환경으로 사용**하는 것도 가능합니다.

{% hint style="success" %}
**요약하자면...**

Virtual Machine은 호스트 머신 내부에 있는 머신입니다. 다른 머신과 정확히 동일하게 작동하며, Operating System과 일부 앱을 가지고 있습니다.

Virtual Machine을 사용하는 데에는 여러 가지 이점이 있습니다:

*   저렴합니다.
*   물리적 공간(보관 공간)을 절약합니다.
*   물리적 머신보다 유지보수가 적습니다.
*   데이터 백업이 단순화됩니다.
*   보안이 강화됩니다.
{% endhint %}

이제 이론을 숙지했으니, 첫 번째 Virtual Machine을 설치하는 실습을 시작할 수 있습니다! 다음 섹션에서 뵙겠습니다.