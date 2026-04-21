---
description: "보너스의 lighttpd + FastCGI, MariaDB, PHP/www-data 배경 이론."
---

# 💡 Explanation — 웹 스택 (보너스)

> **문서 유형**: Explanation. Subject Chapter VII 의 WordPress 스택 구성 요소.

## lighttpd 와 FastCGI

**lighttpd** = 단일 프로세스 이벤트 기반 경량 HTTP 서버. 메모리 ~30 MB. 설정 `/etc/lighttpd/lighttpd.conf`.

**왜 NGINX/Apache 대신?** — Subject SC-38 이 NGINX, Apache2 를 명시 금지. lighttpd 는 두 조건을 모두 만족하는 경량 대안.

### CGI vs FastCGI

**CGI** (Common Gateway Interface):
- 요청마다 새 프로세스 `fork()` + 환경 변수 + stdin 으로 전달
- 프로세스 생성 오버헤드가 매 요청마다 발생 → 느림

**FastCGI**:
- 워커 프로세스를 **상주** 시키고 Unix socket/TCP 로 요청 전달
- 프로세스 재사용 → **10~100 배** 성능 향상
- **PHP-FPM** (FastCGI Process Manager) = PHP 용 독립 데몬

Debian 설정 흐름:
```bash
lighttpd-enable-mod fastcgi          # /etc/lighttpd/conf-enabled/ 에 심링크
lighttpd-enable-mod fastcgi-php      # PHP 바인딩
systemctl restart lighttpd
```

### 30 초 답변
"lighttpd 는 NGINX·Apache 금지 제약 하에서 가장 가벼운 HTTP 서버입니다. FastCGI 를 통해 PHP 인터프리터를 상주 프로세스로 유지해 요청마다 fork 하는 CGI 대비 10~100 배 빠릅니다."

### 더 깊이
- `"FastCGI vs CGI performance benchmark"`
- `"PHP-FPM lighttpd setup"`
- `"lighttpd.conf fastcgi module"`

---

## MariaDB (MySQL 의 fork)

**MariaDB** = MySQL 의 **drop-in replacement** fork.

탄생 배경: 2009 Oracle 의 MySQL 인수 후 원 개발자 Michael "Monty" Widenius 가 라이선스·방향성 우려로 분기.

- 대부분의 쿼리/프로토콜/커넥터 **호환** — WordPress, Drupal 등 MySQL 용 앱이 그대로 돔.
- Storage engine: InnoDB (공용) + Aria, ColumnStore, TokuDB (MariaDB 전용)
- **Debian 12+** 에서 `default-mysql-server` 메타 패키지가 **MariaDB 를 가리킴** → Debian 표준.

관리는 `mariadb` 또는 `mysql` 클라이언트 모두 가능.

### mysql_secure_installation
설치 직후 보안 기본값 강화 대화형 스크립트:
- root 비번 설정
- anonymous user 제거
- remote root 로그인 금지
- test 데이터베이스 제거
- 권한 테이블 리로드

Born2beRoot 에서 반드시 실행.

### 30 초 답변
"MariaDB 는 2009 년 Oracle 의 MySQL 인수 이후 원 개발자가 만든 fork 로, MySQL 과 프로토콜·쿼리가 호환됩니다. Debian 12 부터 `default-mysql-server` 가 MariaDB 를 가리켜 사실상 Debian 표준 DB 입니다."

### 더 깊이
- `"MariaDB vs MySQL differences"`
- `"MariaDB storage engines"`
- `"Debian default-mysql-server MariaDB"`

---

## PHP 와 www-data

**PHP** = 서버 측 스크립트 언어. `.php` 파일을 인터프리터가 실행해 HTML 출력.

### www-data 계정

Debian 에서 lighttpd/Apache 는 **`www-data`** 라는 전용 계정으로 실행 (UID 33, `/etc/passwd`).

왜 별도 계정? — **최소 권한 원칙**. 웹 서버 프로세스에 취약점이 있어도 www-data 권한으로만 피해 제한. root 로 실행하면 탈취 시 시스템 전체 위험.

### /var/www 의 소유권

`/var/www/html/` 의 소유자·그룹이 `www-data` 가 아니면:
- PHP 가 파일 쓰기 실패 → WordPress 업로드·캐시 실패
- lighttpd 가 읽기 실패 → 404

설치 마지막 단계:
```bash
chown -R www-data:www-data /var/www/html
```

### 30 초 답변
"PHP 는 `.php` 파일을 인터프리터가 실행해 HTML 을 만드는 서버 언어입니다. lighttpd 는 `www-data` 라는 최소 권한 계정으로 실행되어, 웹 서버 취약점이 있어도 시스템 전체 피해를 막습니다. `/var/www` 소유권을 `www-data` 로 맞추지 않으면 업로드·캐시가 실패합니다."

### 더 깊이
- `"www-data user Linux purpose"`
- `"PHP file permissions chown"`
- `"PHP-FPM pool user group"`

---

## 왜 "보너스는 필수 완벽 시에만" (SC-Bonus Warning)?

Subject Chapter VII 경고: *"The bonus part will only be assessed if the mandatory part is PERFECT."*

이유 추측:
- 필수 부분이 불완전하면 보너스로 점수를 보충받는 것이 **필수 학습의 우회**
- 운영자 입장에서 "핵심 보안 인프라 (sudo, UFW, PAM) 가 완벽하지 않은 서버에 WordPress 를 올리는 건 위험" 이라는 실무 감각 전수

평가 전략: 필수 28 조항 모두 자신 있을 때만 보너스 진행. 그 전에 시간이 부족하면 보너스 **포기**.

## 관련 문서

- [튜토리얼 E8 (보너스 안내만)](../00-tutorial/install-your-first-vm.md#e8-보너스-wordpress--netdata)
- [How-to: WordPress + Netdata 전체](../01-how-to/add-wordpress-and-netdata.md)
- [Subject SC-36~38](../02-reference/subject-v5.2-clauses.md#chapter-vii)
