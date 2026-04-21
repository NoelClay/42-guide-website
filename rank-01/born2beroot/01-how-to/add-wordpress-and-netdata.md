---
description: "보너스 2 서비스 구축 — WordPress (lighttpd + MariaDB + PHP) 와 자유 선택 서비스 Netdata."
---

# 🎯 How-to — 보너스: WordPress + Netdata

> **문서 유형**: How-to. 보너스 5 점 (파티션 2 + WordPress 2 + 자유 1) 중 후 2 가지.
> **전제**: 필수 부분 완료. `/var` 에 2 GB 이상 여유. `/srv` 에 1 GB 여유.
> **제약**: NGINX, Apache2 사용 절대 금지 (SC-38).

## 전체 흐름

1. [WordPress 스택 설치](#wordpress) — lighttpd + MariaDB + PHP
2. [MariaDB 데이터베이스 생성](#mariadb-setup)
3. [WordPress 파일 배치 + 초기 설정](#wp-install)
4. [Netdata 설치](#netdata)
5. [UFW 추가 포트 허용](#ufw-ports)

---

## <a id="wordpress"></a>1. WordPress 스택 설치

```bash
sudo apt update
sudo apt install lighttpd mariadb-server mariadb-client \
    php php-cgi php-mysql php-fpm php-common php-mbstring \
    php-xml php-curl php-zip wget unzip -y
```

**apache2 가 따라 설치되지 않는지 확인**:

```bash
dpkg -l | grep -E "^ii.*(apache2|nginx)"
# 출력 없어야 함
```

만약 있으면 → [apt 충돌 해결](recover-from-lockout.md#apt-conflict).

## lighttpd FastCGI 모듈 활성화

```bash
sudo lighttpd-enable-mod fastcgi
sudo lighttpd-enable-mod fastcgi-php
sudo systemctl restart lighttpd
sudo systemctl status lighttpd | head -5
```

`Active: active (running)` 확인.

---

## <a id="mariadb-setup"></a>2. MariaDB 데이터베이스 생성

```bash
sudo mysql_secure_installation
```

대화형 프롬프트:
- `Enter current password for root`: Enter (비어 있음)
- `Switch to unix_socket authentication`: `n`
- `Change the root password?`: `Y` → 강한 비번
- 나머지 모두 `Y` (anonymous user 제거, remote root 금지, test DB 제거, 권한 리로드)

DB + 사용자 생성:

```bash
sudo mysql -u root -p
```

MariaDB 프롬프트에서:

```sql
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'StrongPass2026!';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## <a id="wp-install"></a>3. WordPress 배치

```bash
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar xzf latest.tar.gz
sudo cp -R wordpress/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html
```

### lighttpd 설정 수정 (포트 80)

기본 80 포트가 그대로면 OK. 다른 포트를 원하면:

```bash
sudo nano /etc/lighttpd/lighttpd.conf
```

`server.port` 줄이 있으면 원하는 포트로 변경 후 `systemctl restart lighttpd`.

### wp-config.php 설정

```bash
cd /var/www/html
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```

아래 4 필드 수정:

```php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'StrongPass2026!');
define('DB_HOST', 'localhost');
```

### 설치 마법사 접속

호스트에서 VBox 포트 포워딩 (Settings → Network → Advanced → Port Forwarding) 에 추가:

| Name | Host Port | Guest Port |
|---|---|---|
| HTTP | 8080 | 80 |

브라우저 → `http://localhost:8080` → WordPress 설치 마법사 표시. 설치 완료.

---

## <a id="netdata"></a>4. Netdata 설치 (자유 선택 서비스)

**Netdata 선택 이유 (평가자 Q&A 용)**:
- 경량 시스템 모니터링 대시보드 (NGINX/Apache 불필요, 자체 포트 사용).
- 실시간 메트릭 (CPU/메모리/디스크/네트워크) 을 브라우저로 시각화.
- Born2beRoot 의 monitoring.sh 와 상보적 관계 — CLI vs 웹 UI.

### 설치

```bash
bash <(curl -Ss https://my-netdata.io/kickstart.sh) --dont-wait --stable-channel
```

(또는 apt 버전):

```bash
sudo apt install netdata -y
```

설치 후 자동으로 포트 19999 리스닝.

### 서비스 확인

```bash
sudo systemctl status netdata | head -5
ss -tunlp | grep 19999
```

### 외부 접속 허용 (기본은 localhost only)

```bash
sudo nano /etc/netdata/netdata.conf
```

`[web]` 섹션 찾아 수정:
```
bind to = 0.0.0.0
```

재시작:
```bash
sudo systemctl restart netdata
```

---

## <a id="ufw-ports"></a>5. UFW 포트 허용

현재 4242 만 열림. 추가:

```bash
sudo ufw allow 80/tcp            # lighttpd (또는 8080)
sudo ufw allow 19999/tcp         # netdata
sudo ufw status verbose
```

호스트 VBox 포트 포워딩에 19999 도 추가:

| Name | Host Port | Guest Port |
|---|---|---|
| Netdata | 19999 | 19999 |

호스트 브라우저 → `http://localhost:19999` → Netdata 대시보드.

---

## 평가 시연 (Gate PE-B 용)

```bash
# WordPress
systemctl is-active lighttpd mariadb
# 호스트 브라우저: http://localhost:8080 → WP 사이트

# Netdata
systemctl is-active netdata
ss -tunlp | grep 19999
# 호스트 브라우저: http://localhost:19999

# 포트 상태
sudo ufw status | grep -E "4242|80|19999"
```

## 주의사항

- **NGINX/Apache2 금지** 재확인: `dpkg -l | grep -E "apache2|nginx"` 출력 없어야 함.
- **`/var` 공간**: WordPress 파일 + MariaDB 데이터 → `/var/lib/mysql` 비대화. 최소 2 GB 여유. 부족하면 [LV 확장](extend-lv-capacity.md).
- **평가 후 정리**: 보너스는 평가마다 동일 상태로 시연되어야 함. 테스트용 사용자·포스트는 삭제.

## 관련 문서

- [Subject v5.2 SC-36 ~ SC-38 (Bonus)](../02-reference/subject-v5.2-clauses.md#chapter-vii)
- [설명: 웹 스택 (lighttpd/FastCGI/PHP/MariaDB)](../03-explanation/web-stack-bonus.md)
- [LV 확장](extend-lv-capacity.md)
- [apt 충돌 복구](recover-from-lockout.md#apt-conflict)
