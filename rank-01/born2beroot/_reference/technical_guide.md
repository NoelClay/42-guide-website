개발자가 문서를 쓰는 이유
좋은 README vs 나쁜 README
나쁜 README의 특징
사용 예제
아키텍처
기여 방법
라이선스
OpenAPI 검증 도구
Architecture Decision Records (ADR)
ADR 템플릿
Diátaxis 프레임워크: 문서의 4가지 유형
Tutorial: 완전 초보자를 위한 단계별 가이드
단계 2: app.py 생성
단계 3: 실행
결과 확인
문서화 도구: Docusaurus, GitBook, Mintlify
Docusaurus (오픈소스, React 기반)
GitBook (클라우드)
Mintlify (API 문서 특화)
일반적인 문서 작성 안티패턴
1. "시간이 없어서" 문서를 안 쓰는 경우
2. 코드 주석만 문서라고 생각하는 경우
3. 문서를 코드와 별도로 관리하는 경우
4. 한 문서에 모든 걸 넣으려는 경우
실전 체크리스트
결론
참고자료


 개발자가 문서를 쓰는 이유
"코드가 곧 문서다"라는 말을 들어본 적 있나요? 안타깝게도 이건 반은 맞고 반은 틀렸습니다. 코드는 '무엇을' 하는지는 명확히 보여주지만, '왜' 하는지, '어떻게 사용하는지'는 절대 설명할 수 없거든요.

좋은 기술 문서는:

온보딩 시간을 단축합니다 - 새 팀원이 1주일이 아닌 1일 만에 생산성을 내기 시작합니다
버그를 줄입니다 - API 사용법이 명확하면 오남용이 줄어듭니다
유지보수 비용을 절감합니다 - 6개월 뒤 코드를 다시 볼 때 자신 있게 수정할 수 있습니다
신뢰도를 높입니다 - 잘 정리된 문서는 프로젝트 전체를 신뢰하게 만듭니다
실제로 Google, Meta, Amazon 같은 대형 기술 회사들은 엔지니어의 평가에 "문서 작성 능력"을 포함시킵니다. 이제 문서 작성은 선택이 아닌 필수 능력입니다.

 좋은 README vs 나쁜 README
 나쁜 README의 특징
# Project X

This is a project.

## Usage

```bash
npm install
npm start
```
That's it!


이 README의 문제점:
- **설치했는데 뭘 해야 하는가?** 불명확
- **어떤 문제를 푸는가?** 모름
- **어떤 기술 스택인가?** 모름
- **누가 사용할 수 있는가?** 모름

### 좋은 README의 구조

```markdown
# Project Name

**한 문장 설명**: 이 프로젝트가 푸는 구체적인 문제

## 주요 기능

- Feature 1
- Feature 2
- Feature 3

## 빠른 시작

### 설치 사전 조건
- Node.js 18.0+
- npm 9.0+

### 설치 및 실행

```bash
git clone https://github.com/user/project.git
cd project
npm install
npm run dev
 사용 예제
const project = new Project()
project.doSomething()
 아키텍처
[ASCII 다이어그램 또는 이미지]

 기여 방법
 라이선스

좋은 README를 위한 체크리스트:
- [ ] 프로젝트 목표가 첫 3줄에 명확히 나오는가?
- [ ] 최소 한 가지 사용 예제가 있는가?
- [ ] 설치 사전 조건이 명시되어 있는가?
- [ ] 구조도 또는 다이어그램이 포함되었는가?
- [ ] 기여 방법이 명확한가?
- [ ] 트러블슈팅 섹션이 있는가?

## API 문서: OpenAPI와 Swagger

API 문서는 단순 텍스트가 아닌 **기계가 읽을 수 있는 형식**으로 작성되어야 합니다. OpenAPI(구 Swagger) 표준을 사용하면:

1. **문서와 코드가 동기화됩니다** - 코드가 변하면 문서도 자동으로 업데이트
2. **클라이언트 코드를 자동 생성할 수 있습니다**
3. **인터랙티브 테스팅이 가능합니다**

### OpenAPI 3.0 기본 구조

```yaml
openapi: 3.0.0
info:
  title: 사용자 관리 API
  version: 1.0.0
  description: 사용자 정보를 관리하는 API

servers:
  - url: https://api.example.com/v1
    description: 프로덕션

paths:
  /users:
    get:
      summary: 모든 사용자 조회
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
          description: 반환할 최대 사용자 수
      responses:
        '200':
          description: 성공
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'

    post:
      summary: 새 사용자 생성
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':
          description: 사용자 생성 완료
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
          format: email
      required:
        - id
        - name
        - email
 OpenAPI 검증 도구
Swagger UI: 브라우저에서 API를 테스트할 수 있는 UI
ReDoc: 깔끔한 참고 문서 자동 생성
OpenAPI Linter: 문서의 일관성 검증
 Architecture Decision Records (ADR)
ADR은 아키텍처 선택지와 그 이유를 기록하는 문서입니다. 마이크로서비스로 마이그레이션하든, 새 라이브러리를 도입하든, 이 결정을 기록해야 합니다.

 ADR 템플릿
# ADR-001: React로 프론트엔드 마이그레이션

## 상태

승인됨

## 배경

기존 jQuery 코드는 유지보수가 어렵고, 신입 개발자들이 학습하기 어려움.
현대적인 프레임워크로 이동해야 함.

## 선택지

1. **React**: 커뮤니티 큼, 다양한 라이브러리, 채용 용이
2. **Vue**: 학습곡선 낮음, 문서 우수
3. **Angular**: 풀 프레임워크, 대규모 프로젝트 적합

## 결정

**React를 선택**

## 근거

- 팀 경험: 일부 팀원이 이미 React 경험 보유
- 채용: React 개발자가 시장에 많음
- 라이브러리 생태계: Next.js, TanStack Query 등 우수한 도구들
- 커뮤니티: Stack Overflow, GitHub에서 답변 찾기 쉬움

## 트레이드오프

- 번들 크기가 Vue보다 큼
- 학습곡선이 Vue보다 가파름
- 상태 관리 라이브러리 추가 필요 (Redux, Zustand 등)

## 결과 (6개월 후 추가 작성)

개발 생산성이 40% 증가했고, 신입 온보딩 시간이 2주 단축됨.
ADR의 장점:

결정 이력을 남깁니다 - 왜 이 기술을 선택했는지 6개월 뒤에도 알 수 있습니다
재검토 트리거가 됩니다 - 상황이 변하면 ADR을 다시 읽고 재평가할 수 있습니다
새 팀원 온보딩이 쉬워집니다 - "왜 React를 쓰나요?" 질문에 이미 답이 있습니다
 Diátaxis 프레임워크: 문서의 4가지 유형
기술 문서를 작성할 때 가장 자주 헷갈리는 부분은 어떤 문서를 만들어야 하는가입니다. Diátaxis 프레임워크는 문서를 4가지 명확한 유형으로 분류합니다:

유형	대상	목표	특징
Tutorial	입문자	학습하기	순서대로, 모든 단계 포함, 결과 보장
How-to Guide	경험자	특정 작업 완료	특정 문제 해결, 독립적 존재
Reference	개발자	정보 찾기	구조적, 검색 가능, 완벽한
Explanation	학생	이해하기	배경 지식, 왜 그런지 설명
 Tutorial: 완전 초보자를 위한 단계별 가이드
# 튜토리얼: 첫 번째 웹 API 만들기

이 튜토리얼에서는 15분 안에 작동하는 웹 API를 만들 것입니다.

## 필요한 것

- Python 3.9+
- 터미널

## 단계 1: Flask 설치

```bash
pip install flask
```
 단계 2: app.py 생성
from flask import Flask

app = Flask(__name__)

@app.route('/api/hello', methods=['GET'])
def hello():
    return {'message': 'Hello, World!'}

if __name__ == '__main__':
    app.run(debug=True)
 단계 3: 실행
python app.py
 결과 확인
브라우저에서 http://localhost:5000/api/hello 열기


### How-to Guide: 경험자를 위한 문제 해결 가이드

```markdown
# 방법: CORS 에러 해결하기

프론트엔드에서 API를 호출할 때 CORS 에러가 나면:

```python
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # 모든 도메인에서 요청 허용
또는 특정 도메인만:

CORS(app, origins=['https://example.com'])

### Reference: 완전한 API 레퍼런스

```markdown
# API 레퍼런스

## GET /api/users/{id}

사용자 정보를 조회합니다.

**파라미터:**
- `id` (integer, 필수): 사용자 ID

**응답:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
에러:

404: 사용자를 찾을 수 없음

### Explanation: 왜 이렇게 설계했는가

```markdown
# 설명: 마이크로서비스 아키텍처 선택 이유

마이크로서비스는 하나의 거대한 애플리케이션을 작은 독립적인 서비스들로 나눕니다.
이를 통해 팀의 속도, 확장성, 유연성이 높아집니다.

하지만 복잡도도 증가합니다...
 문서화 도구: Docusaurus, GitBook, Mintlify
 Docusaurus (오픈소스, React 기반)
npx create-docusaurus@latest my-docs classic
cd my-docs
npm run start
장점:

React와 MDX 완벽 지원
정적 사이트로 빌드되어 배포 간편
버전 관리 기능 내장
 GitBook (클라우드)
협업 기능 우수
아름다운 기본 디자인
비용 발생 가능
 Mintlify (API 문서 특화)
OpenAPI 통합
터미널처럼 보이는 코드 블록
현대적 디자인
 일반적인 문서 작성 안티패턴
 1. "시간이 없어서" 문서를 안 쓰는 경우
현실: 지금 5시간을 들여 문서를 쓰면, 미래에 50시간을 절약합니다.

 2. 코드 주석만 문서라고 생각하는 경우
코드 주석은 "왜"가 아닌 "뭘"을 설명합니다. 문서는 큰 그림을 보여줘야 합니다.

 3. 문서를 코드와 별도로 관리하는 경우
해결책: 문서를 저장소 안에 두고, PR 검토 시 함께 검토하세요.

docs/
├── README.md
├── api/
│   ├── users.md
│   └── posts.md
├── architecture/
│   └── decisions/
│       ├── 001-react-migration.md
│       └── 002-microservices.md
└── guides/
    ├── setup.md
    └── contributing.md
 4. 한 문서에 모든 걸 넣으려는 경우
좋은 구조:

README: 프로젝트 개요 (1-2분)
CONTRIBUTING: 기여 방법 (링크)
docs/SETUP: 개발 환경 세팅 (10분)
docs/ARCHITECTURE: 시스템 설계 (30분)
API_REFERENCE: API 스펙 (자동 생성)
 실전 체크리스트
문서를 쓸 때마다 확인하세요:

 Title은 명확한가? "API 문서"보다 "사용자 관리 API 레퍼런스"가 낫습니다
 대상 독자가 명확한가? 이 문서는 누가 읽나요?
 실행 가능한가? 단계를 따라 실제로 작동하는가?
 예제가 있는가? 추상적 설명보다 구체적 예제가 낫습니다
 스크린샷/다이어그램이 있는가? 시각화는 1000단어의 가치가 있습니다
 최신 정보인가? 몇 개월 된 문서라면 버전을 명시하세요
 검색 가능한가? 관련 키워드가 포함되어 있나요?
 링크가 작동하는가? 특히 외부 링크를 확인하세요
 누가 유지보수할 건가? 책임자를 명시하세요
 결론
기술 문서는 선택이 아닌 투자입니다. 좋은 문서는:

팀의 속도를 높입니다
버그를 줄입니다
신입 개발자의 온보딩 시간을 단축합니다
경력 발전의 증거가 됩니다
이번 주부터 README를 다시 읽고, 하나의 ADR을 작성해 보세요. 여러분의 미래 자신이 감사할 것입니다.

 참고자료
The Diátaxis Documentation Framework
OpenAPI Specification 3.0
Architecture Decision Records (ADR) - Michael Nygard
GitHub README Best Practices
Write the Docs Community Guides

— Chaos and Order (Youngju Kim)
📎 https://www.youngju.dev/blog/culture/2026-03-16-technical-documentation-writing-guide개발자가 문서를 쓰는 이유
좋은 README vs 나쁜 README
나쁜 README의 특징
사용 예제
아키텍처
기여 방법
라이선스
OpenAPI 검증 도구
Architecture Decision Records (ADR)
ADR 템플릿
Diátaxis 프레임워크: 문서의 4가지 유형
Tutorial: 완전 초보자를 위한 단계별 가이드
단계 2: app.py 생성
단계 3: 실행
결과 확인
문서화 도구: Docusaurus, GitBook, Mintlify
Docusaurus (오픈소스, React 기반)
GitBook (클라우드)
Mintlify (API 문서 특화)
일반적인 문서 작성 안티패턴
1. "시간이 없어서" 문서를 안 쓰는 경우
2. 코드 주석만 문서라고 생각하는 경우
3. 문서를 코드와 별도로 관리하는 경우
4. 한 문서에 모든 걸 넣으려는 경우
실전 체크리스트
결론
참고자료


 개발자가 문서를 쓰는 이유
"코드가 곧 문서다"라는 말을 들어본 적 있나요? 안타깝게도 이건 반은 맞고 반은 틀렸습니다. 코드는 '무엇을' 하는지는 명확히 보여주지만, '왜' 하는지, '어떻게 사용하는지'는 절대 설명할 수 없거든요.

좋은 기술 문서는:

온보딩 시간을 단축합니다 - 새 팀원이 1주일이 아닌 1일 만에 생산성을 내기 시작합니다
버그를 줄입니다 - API 사용법이 명확하면 오남용이 줄어듭니다
유지보수 비용을 절감합니다 - 6개월 뒤 코드를 다시 볼 때 자신 있게 수정할 수 있습니다
신뢰도를 높입니다 - 잘 정리된 문서는 프로젝트 전체를 신뢰하게 만듭니다
실제로 Google, Meta, Amazon 같은 대형 기술 회사들은 엔지니어의 평가에 "문서 작성 능력"을 포함시킵니다. 이제 문서 작성은 선택이 아닌 필수 능력입니다.

 좋은 README vs 나쁜 README
 나쁜 README의 특징
# Project X

This is a project.

## Usage

```bash
npm install
npm start
```
That's it!


이 README의 문제점:
- **설치했는데 뭘 해야 하는가?** 불명확
- **어떤 문제를 푸는가?** 모름
- **어떤 기술 스택인가?** 모름
- **누가 사용할 수 있는가?** 모름

### 좋은 README의 구조

```markdown
# Project Name

**한 문장 설명**: 이 프로젝트가 푸는 구체적인 문제

## 주요 기능

- Feature 1
- Feature 2
- Feature 3

## 빠른 시작

### 설치 사전 조건
- Node.js 18.0+
- npm 9.0+

### 설치 및 실행

```bash
git clone https://github.com/user/project.git
cd project
npm install
npm run dev
 사용 예제
const project = new Project()
project.doSomething()
 아키텍처
[ASCII 다이어그램 또는 이미지]

 기여 방법
 라이선스

좋은 README를 위한 체크리스트:
- [ ] 프로젝트 목표가 첫 3줄에 명확히 나오는가?
- [ ] 최소 한 가지 사용 예제가 있는가?
- [ ] 설치 사전 조건이 명시되어 있는가?
- [ ] 구조도 또는 다이어그램이 포함되었는가?
- [ ] 기여 방법이 명확한가?
- [ ] 트러블슈팅 섹션이 있는가?

## API 문서: OpenAPI와 Swagger

API 문서는 단순 텍스트가 아닌 **기계가 읽을 수 있는 형식**으로 작성되어야 합니다. OpenAPI(구 Swagger) 표준을 사용하면:

1. **문서와 코드가 동기화됩니다** - 코드가 변하면 문서도 자동으로 업데이트
2. **클라이언트 코드를 자동 생성할 수 있습니다**
3. **인터랙티브 테스팅이 가능합니다**

### OpenAPI 3.0 기본 구조

```yaml
openapi: 3.0.0
info:
  title: 사용자 관리 API
  version: 1.0.0
  description: 사용자 정보를 관리하는 API

servers:
  - url: https://api.example.com/v1
    description: 프로덕션

paths:
  /users:
    get:
      summary: 모든 사용자 조회
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
          description: 반환할 최대 사용자 수
      responses:
        '200':
          description: 성공
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'

    post:
      summary: 새 사용자 생성
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':
          description: 사용자 생성 완료
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
          format: email
      required:
        - id
        - name
        - email
 OpenAPI 검증 도구
Swagger UI: 브라우저에서 API를 테스트할 수 있는 UI
ReDoc: 깔끔한 참고 문서 자동 생성
OpenAPI Linter: 문서의 일관성 검증
 Architecture Decision Records (ADR)
ADR은 아키텍처 선택지와 그 이유를 기록하는 문서입니다. 마이크로서비스로 마이그레이션하든, 새 라이브러리를 도입하든, 이 결정을 기록해야 합니다.

 ADR 템플릿
# ADR-001: React로 프론트엔드 마이그레이션

## 상태

승인됨

## 배경

기존 jQuery 코드는 유지보수가 어렵고, 신입 개발자들이 학습하기 어려움.
현대적인 프레임워크로 이동해야 함.

## 선택지

1. **React**: 커뮤니티 큼, 다양한 라이브러리, 채용 용이
2. **Vue**: 학습곡선 낮음, 문서 우수
3. **Angular**: 풀 프레임워크, 대규모 프로젝트 적합

## 결정

**React를 선택**

## 근거

- 팀 경험: 일부 팀원이 이미 React 경험 보유
- 채용: React 개발자가 시장에 많음
- 라이브러리 생태계: Next.js, TanStack Query 등 우수한 도구들
- 커뮤니티: Stack Overflow, GitHub에서 답변 찾기 쉬움

## 트레이드오프

- 번들 크기가 Vue보다 큼
- 학습곡선이 Vue보다 가파름
- 상태 관리 라이브러리 추가 필요 (Redux, Zustand 등)

## 결과 (6개월 후 추가 작성)

개발 생산성이 40% 증가했고, 신입 온보딩 시간이 2주 단축됨.
ADR의 장점:

결정 이력을 남깁니다 - 왜 이 기술을 선택했는지 6개월 뒤에도 알 수 있습니다
재검토 트리거가 됩니다 - 상황이 변하면 ADR을 다시 읽고 재평가할 수 있습니다
새 팀원 온보딩이 쉬워집니다 - "왜 React를 쓰나요?" 질문에 이미 답이 있습니다
 Diátaxis 프레임워크: 문서의 4가지 유형
기술 문서를 작성할 때 가장 자주 헷갈리는 부분은 어떤 문서를 만들어야 하는가입니다. Diátaxis 프레임워크는 문서를 4가지 명확한 유형으로 분류합니다:

유형	대상	목표	특징
Tutorial	입문자	학습하기	순서대로, 모든 단계 포함, 결과 보장
How-to Guide	경험자	특정 작업 완료	특정 문제 해결, 독립적 존재
Reference	개발자	정보 찾기	구조적, 검색 가능, 완벽한
Explanation	학생	이해하기	배경 지식, 왜 그런지 설명
 Tutorial: 완전 초보자를 위한 단계별 가이드
# 튜토리얼: 첫 번째 웹 API 만들기

이 튜토리얼에서는 15분 안에 작동하는 웹 API를 만들 것입니다.

## 필요한 것

- Python 3.9+
- 터미널

## 단계 1: Flask 설치

```bash
pip install flask
```
 단계 2: app.py 생성
from flask import Flask

app = Flask(__name__)

@app.route('/api/hello', methods=['GET'])
def hello():
    return {'message': 'Hello, World!'}

if __name__ == '__main__':
    app.run(debug=True)
 단계 3: 실행
python app.py
 결과 확인
브라우저에서 http://localhost:5000/api/hello 열기


### How-to Guide: 경험자를 위한 문제 해결 가이드

```markdown
# 방법: CORS 에러 해결하기

프론트엔드에서 API를 호출할 때 CORS 에러가 나면:

```python
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # 모든 도메인에서 요청 허용
또는 특정 도메인만:

CORS(app, origins=['https://example.com'])

### Reference: 완전한 API 레퍼런스

```markdown
# API 레퍼런스

## GET /api/users/{id}

사용자 정보를 조회합니다.

**파라미터:**
- `id` (integer, 필수): 사용자 ID

**응답:**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
에러:

404: 사용자를 찾을 수 없음

### Explanation: 왜 이렇게 설계했는가

```markdown
# 설명: 마이크로서비스 아키텍처 선택 이유

마이크로서비스는 하나의 거대한 애플리케이션을 작은 독립적인 서비스들로 나눕니다.
이를 통해 팀의 속도, 확장성, 유연성이 높아집니다.

하지만 복잡도도 증가합니다...
 문서화 도구: Docusaurus, GitBook, Mintlify
 Docusaurus (오픈소스, React 기반)
npx create-docusaurus@latest my-docs classic
cd my-docs
npm run start
장점:

React와 MDX 완벽 지원
정적 사이트로 빌드되어 배포 간편
버전 관리 기능 내장
 GitBook (클라우드)
협업 기능 우수
아름다운 기본 디자인
비용 발생 가능
 Mintlify (API 문서 특화)
OpenAPI 통합
터미널처럼 보이는 코드 블록
현대적 디자인
 일반적인 문서 작성 안티패턴
 1. "시간이 없어서" 문서를 안 쓰는 경우
현실: 지금 5시간을 들여 문서를 쓰면, 미래에 50시간을 절약합니다.

 2. 코드 주석만 문서라고 생각하는 경우
코드 주석은 "왜"가 아닌 "뭘"을 설명합니다. 문서는 큰 그림을 보여줘야 합니다.

 3. 문서를 코드와 별도로 관리하는 경우
해결책: 문서를 저장소 안에 두고, PR 검토 시 함께 검토하세요.

docs/
├── README.md
├── api/
│   ├── users.md
│   └── posts.md
├── architecture/
│   └── decisions/
│       ├── 001-react-migration.md
│       └── 002-microservices.md
└── guides/
    ├── setup.md
    └── contributing.md
 4. 한 문서에 모든 걸 넣으려는 경우
좋은 구조:

README: 프로젝트 개요 (1-2분)
CONTRIBUTING: 기여 방법 (링크)
docs/SETUP: 개발 환경 세팅 (10분)
docs/ARCHITECTURE: 시스템 설계 (30분)
API_REFERENCE: API 스펙 (자동 생성)
 실전 체크리스트
문서를 쓸 때마다 확인하세요:

 Title은 명확한가? "API 문서"보다 "사용자 관리 API 레퍼런스"가 낫습니다
 대상 독자가 명확한가? 이 문서는 누가 읽나요?
 실행 가능한가? 단계를 따라 실제로 작동하는가?
 예제가 있는가? 추상적 설명보다 구체적 예제가 낫습니다
 스크린샷/다이어그램이 있는가? 시각화는 1000단어의 가치가 있습니다
 최신 정보인가? 몇 개월 된 문서라면 버전을 명시하세요
 검색 가능한가? 관련 키워드가 포함되어 있나요?
 링크가 작동하는가? 특히 외부 링크를 확인하세요
 누가 유지보수할 건가? 책임자를 명시하세요
 결론
기술 문서는 선택이 아닌 투자입니다. 좋은 문서는:

팀의 속도를 높입니다
버그를 줄입니다
신입 개발자의 온보딩 시간을 단축합니다
경력 발전의 증거가 됩니다
이번 주부터 README를 다시 읽고, 하나의 ADR을 작성해 보세요. 여러분의 미래 자신이 감사할 것입니다.

 참고자료
The Diátaxis Documentation Framework
OpenAPI Specification 3.0
Architecture Decision Records (ADR) - Michael Nygard
GitHub README Best Practices
Write the Docs Community Guides

— Chaos and Order (Youngju Kim)
📎 https://www.youngju.dev/blog/culture/2026-03-16-technical-documentation-writing-guide