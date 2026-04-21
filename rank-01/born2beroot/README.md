---
description: "이 프로젝트의 목표는 \"Virtual Box\"를 사용하여 Virtual Machine을 생성하는 것입니다."
---

# Born2beRoot

저처럼 **컴퓨터에 대해 아무것도 모르는** 분이라면, <mark style="color:green;">환영합니다!</mark>

B2BR 주제를 처음 읽었을 때 저는 완전히 길을 잃었고 어디서부터 시작해야 할지 몰랐습니다... 여러분이 저만큼 길을 잃지 않도록 최선을 다해 돕겠습니다. 그리고 잊지 마십시오. 자신이 무엇을 하고 있는지 최대한 이해하려고 노력해야 합니다 :)

**이제 C 프로그래밍과는 크게 관련이 없는 완전히 새로운 주제를 깊이 탐구해 보겠습니다.**

먼저 주제에서 사용되는 모든 (낯선) 용어들을 검토할 것입니다. 목표는 우리가 무엇을 하는지 이해하고 이 지식을 활용하여 Virtual Machine을 구축할 수 있게 되는 것입니다. 그런 다음 Virtual Machine을 설치하고 주제에서 요구하는 사항들을 수행하며 시작할 것입니다.

{% hint style="warning" %}
이 프로젝트에서는 Virtual Machine 생성뿐만 아니라 이론적인 요소(정의 및 개념)에 대해서도 평가받게 됩니다. 따라서 모든 것을 이해하고 있는지 확인해야 합니다!
{% endhint %}

---

## 📘 New v5.2 (Diátaxis 구조)

Subject **v5.2** 기준 전면 재구성. [Diátaxis 프레임워크](_reference/diataxis-fr-en-latest.pdf) 에 따라 **목적별 4 사분면** 으로 나뉩니다. 자신의 상황에 맞는 입구로 들어가세요.

<table data-view="cards">
  <thead><tr><th></th><th></th><th></th></tr></thead>
  <tbody>
    <tr>
      <td><h3>🎓 Tutorial</h3><p>처음이라 손잡고 끝까지 가고 싶다</p></td>
      <td><strong>학습 지향</strong> · Happy path 한 경로</td>
      <td><a href="00-tutorial/install-your-first-vm.md">첫 VM 설치 →</a></td>
    </tr>
    <tr>
      <td><h3>🎯 How-to</h3><p>특정 목표만 빨리 달성하고 싶다</p></td>
      <td><strong>목표 지향</strong> · 실무 레시피 5 종</td>
      <td><a href="01-how-to/pass-peer-evaluation.md">평가 통과 →</a></td>
    </tr>
    <tr>
      <td><h3>📚 Reference</h3><p>정확한 스펙·조항을 찾는다</p></td>
      <td><strong>정보 지향</strong> · 사실만, 중립적</td>
      <td><a href="02-reference/subject-v5.2-clauses.md">Subject 조항 →</a></td>
    </tr>
    <tr>
      <td><h3>💡 Explanation</h3><p>왜 이렇게 설계했는지 이해하고 싶다</p></td>
      <td><strong>이해 지향</strong> · 배경·이론</td>
      <td><a href="03-explanation/design-decisions.md">설계 결정 ADR →</a></td>
    </tr>
  </tbody>
</table>

### 전체 페이지 목록

**🎓 Tutorial**
- [첫 Born2beRoot VM 설치](00-tutorial/install-your-first-vm.md)

**🎯 How-to**
- [Peer evaluation 통과](01-how-to/pass-peer-evaluation.md)
- [잠김 상태에서 복구](01-how-to/recover-from-lockout.md)
- [비밀번호 로테이션](01-how-to/rotate-passwords.md)
- [LV 용량 확장](01-how-to/extend-lv-capacity.md)
- [보너스: WordPress + Netdata](01-how-to/add-wordpress-and-netdata.md)

**📚 Reference**
- [Subject v5.2 조항 (41 개)](02-reference/subject-v5.2-clauses.md)
- [Peer Evaluation Protocol](02-reference/peer-eval-protocol.md)
- [설정 파일 스펙](02-reference/config-files.md)
- [명령 치트시트](02-reference/commands-cheatsheet.md)

**💡 Explanation**
- [가상화](03-explanation/virtualization.md)
- [부팅 흐름](03-explanation/boot-sequence.md)
- [저장 스택](03-explanation/storage-stack.md)
- [네트워크 보안](03-explanation/network-security.md)
- [접근 제어](03-explanation/access-control.md)
- [모니터링 · 무결성](03-explanation/monitoring-integrity.md)
- [웹 스택 (보너스)](03-explanation/web-stack-bonus.md)
- [설계 결정 (ADR)](03-explanation/design-decisions.md)

{% hint style="info" %}
**어디부터 읽을까?** — 처음이면 🎓 Tutorial 부터. 평가가 임박하면 🎯 How-to의 "평가 통과" 부터. 특정 사실이 궁금하면 📚 Reference. "왜?" 가 궁금하면 💡 Explanation.
{% endhint %}
