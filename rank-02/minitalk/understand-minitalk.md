---
description: "클라이언트와 서버 형태로 통신 프로그램을 구현해야 합니다."
---

# ▪️ Minitalk 이해하기

### 목표

<details>

<summary>프로젝트별 지침 (2023)</summary>

Client와 Server 형태의 통신 프로그램을 생성해야 합니다.

*   Server가 먼저 시작되어야 하며, 실행 후 자신의 PID를 출력해야 합니다.
*   Client는 두 가지 인자를 받습니다. 1) Server의 PID 2) 보낼 문자열입니다.
*   Client는 인자로 전달된 문자열을 Server로 전송해야 합니다. 문자열을 수신한 후, Server는 이를 출력해야 합니다.
*   Server는 문자열을 상당히 빠르게 표시해야 합니다. '빠르게'란 만약 너무 오래 걸린다고 생각된다면, 그것은 아마도 너무 느린 것임을 의미합니다.
*   Server는 재시작할 필요 없이 여러 Client로부터 연속적으로 문자열을 수신할 수 있어야 합니다.
*   Client와 Server 간의 통신은 오직 UNIX Signals만을 사용하여 이루어져야 합니다.
*   사용할 수 있는 Signal은 SIGUSR1과 SIGUSR2, 단 두 가지뿐입니다.

</details>

Minitalk 프로젝트의 목표는 'minitalk'이라는 통신 프로토콜을 사용하여 Process(= 컴퓨터에서 실행되는 프로그램)들이 서로 통신할 수 있도록 하는 간단한 프로그램을 개발하는 것입니다. 이 프로토콜이 바로 여러분이 코딩해야 할 프로토콜에 해당합니다.

minitalk 통신 프로토콜은 단일 와이어를 통해 일련의 Signals를 사용하여 두 Process 사이에 메시지를 전송하는 것을 포함합니다.

*   "화자/Client"라고 불리는 한 Process는 와이어를 통해 일련의 Signals를 전송함으로써 메시지를 보냅니다.
*   "청자/Server"라고 불리는 다른 Process는 일련의 Signals를 메시지로 해석함으로써 메시지를 수신합니다.

{% hint style="info" %}
**Signals는 Process 간 통신의 한 형태**로, Unix 계열 시스템(예: MacOS 또는 Linux) 및 POSIX 표준을 준수하는 시스템에서 사용됩니다. **Signals는 메시지**, 이벤트 또는 인터럽트로 정의될 수 있습니다. Process가 Signal을 수신하면, 진행 중이던 작업을 멈추고 **특정 조치(action)를 취합니다.**

Minitalk 프로젝트의 맥락에서, Signals는 minitalk 통신 프로토콜을 사용하여 Process 간 메시지를 전송하는 데 사용됩니다.
{% endhint %}

저는 시각적인 것을 선호하므로, 여러분의 이해를 돕기 위해 이 다이어그램을 만들었습니다.

<figure><img src="../../.gitbook/assets/minitalk_scheme.png" alt=""><figcaption><p>minitalk 설명 체계</p></figcaption></figure>

보시다시피, Server에게 메시지를 보내고자 하는 Client가 있습니다. 'Hello'(메시지)는 Server로 직접 전송될 수 없습니다. Client는 메시지를 암호화해야 하며, Server는 이를 표시하기 전에 복호화/해석해야 합니다.

여기서는 일반적인 내용만 다루고, 더 자세한 내용은 "[구현하기](building-the-thing.md)" 섹션에서 설명하겠습니다.

### Process 및 Signal

{% hint style="info" %}
요구 사항보다 조금 더 깊이 들어가겠지만, 이는 여러분이 나중에 수행할 작업을 더 잘 이해하기 위함입니다.
{% endhint %}

**Process와 Signal은 이 프로젝트에서 알아야 할 가장 중요한 용어입니다.** 위에서 이미 이에 대해 조금 설명했지만, 아직 완전히 이해하지 못하셨을 수 있습니다. 이 새로운 개념들을 이해하기 위해 간단한 다른 예시를 들어보겠습니다.

앞서 말씀드렸듯이, 컴퓨터 과학에서 Signal은 특정 이벤트가 발생했음을 알리거나 특정 조치를 취하도록 요청하기 위해 Process로 전송되는 메시지입니다. Process는 컴퓨터에 의해 실행되고 있는 프로그램입니다.

간단한 예를 들어보겠습니다.

컴퓨터에서 실행 중인 프로그램이 있다고 상상해 보세요. 이 프로그램은 여러분의 컴퓨터 백그라운드에서 실행 중인 Google Chrome입니다. 브라우저에 여러 창을 열면, 여러 프로그램이 실행되는 것입니다. 이 프로그램들을 Process라고 부릅니다.

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

이제, 이 프로그램을 중지시키고 싶다고 가정해 봅시다. 해당 브라우저 창을 닫아 간단히 중지할 수 있습니다. 쉽죠. 또는 Process에 Signal을 전송하여 중지할 수도 있습니다. **기억하세요. Signal은 단지 Process에게 특정 작업을 수행하라고 지시하는 것일 뿐입니다!**

여기서 우리는 창을 닫고 싶습니다. 이것이 우리가 Process에 보내고자 하는 Signal이 될 것입니다.

이를 위해 터미널 창에서 "kill" 명령어를 사용하여 Process에 Signal을 보낼 수 있습니다. 이 Signal은 Process에게 스스로 종료하라고 지시합니다. 따라서 이 예시에서 Signal('kill' 명령어)은 특정 조치(Process의 종료)를 취하도록 요청하는 데 사용됩니다.

특정 Process에 Signal을 보내려면 **PID가 필요합니다.** 이는 어떤 Signal을 어떤 Process에 보내야 할지 알 수 있게 해 주므로 유용합니다. 이는 컴퓨터화된 여권과 같습니다. 예시는 다음과 같습니다.

`kill <PID>`

위 명령어를 사용하면 Process를 하나씩 'kill'해야 합니다. 모두 Chrome의 일부이므로, 대신 실행 중인 Chrome 관련 프로그램(Process)을 모두 닫는 단일 명령어를 사용할 수도 있습니다.

`killall chrome`

<details>

<summary>좀 더 깊이 알아보기 위해</summary>

궁금하다면, 다음 명령어를 입력하여 현재 컴퓨터에서 실행 중인 모든 'Google Chrome' 프로그램을 찾을 수 있습니다.

`ps -Af | grep chrome`

그러면 다음과 같이 모든 Chrome Process 목록이 나타날 것입니다.

```
1000      2706     1  2 23:01 ?        00:00:52 /usr/bin/google-chrome-stable
1000      2713  2706  0 23:01 ?        00:00:00 /usr/bin/google-chrome-stable
1000      2714  2706  0 23:01 ?        00:00:00 /opt/google/chrome/chrome-sandbox /opt/google/chrome/chrome --type=zygote
1000      2715  2714  0 23:01 ?        00:00:00 /opt/google/chrome/chrome --type=zygote
1000      2719  2715  0 23:01 ?        00:00:00 /opt/google/chrome/nacl_helper
1000      2720  2715  0 23:01 ?        00:00:00 /opt/google/chrome/chrome --type=zygote
1000      2839  2706  0 23:01 ?        00:00:08 /opt/google/chrome/chrome --type=gpu-process --channel=2706.3.250838429 --supports-dual-gpus=false --gpu-driver-bug-workarounds=0,1,27 --disable-accelerated-video-decode --gpu-vendor-id=0x1002 --gpu-device-id=0x6760 --gpu-driver-vendor=ATI / AMD --gpu-driver-version=13.30
1000      2843  2839  0 23:01 ?        00:00:00 /opt/google/chrome/chrome --type=gpu-process --channel=2706.3.250838429 --supports-dual-gpus=false --gpu-driver-bug-workarounds=0,1,27 --disable-accelerated-video-decode --gpu-vendor-id=0x1002 --gpu-device-id=0x6760 --gpu-driver-vendor=ATI / AMD --gpu-driver-version=13.30
1000      3038  2720  1 23:08 ?        00:00:28 /opt/google/chrome/chrome --type=renderer --lang=en-US --force-[...very long options list]
1000      4505  4441  0 23:40 pts/0    00:00:00 grep --color=auto chromebash
```

_참고: Google Chrome을 열지 않았는데도 한 줄이 나타난다면, 이는 위 명령어(grep chrome)로 호출했기 때문입니다._

두 번째 열에는 Process의 Process Identifier (PID)가 포함됩니다. 따라서 2706, 2713 또는 2720은 모두 특정 Process의 PID에 해당합니다.

</details>

이제 기본 개념에 대해 더 잘 이해하셨기를 바랍니다! 이제 마지막 이론적 부분으로 넘어가겠습니다. 이 프로젝트를 완수하기 위해, 여러분은 이러한 컴퓨터 Signal을 관리하는 데 사용되는 [새로운 함수](functions-used.md)들을 이해하고 사용해야 합니다: `signal()`, `sigemptyset()`, `sigaddset()`, `sigaction()`, `kill()`, `pause()`, 그리고 `sleep()` 입니다.