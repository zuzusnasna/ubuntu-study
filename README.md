# Ubuntu / Linux 학습 기록

Ubuntu 환경에서 **Linux 기초 → 서버 관리 → 배포 환경 구축**으로 이어지는 학습 내용을 날짜별로 기록합니다.

> 학습 목표: Linux 명령어와 서버 환경을 이해하고, 이후 Java/Spring 애플리케이션을 Linux 서버에 배포할 수 있는 기반을 만든다.

---

## 📚 날짜별 학습 목차

| 날짜 | 주제 | 주요 키워드 | 학습 내용 |
|---|---|---|---|
| [2026-09-11](./2026-09-11/) | Linux 기초 / 파일 시스템 | `Terminal` `Shell` `Bash` `Shell Script` `ls` `cd` `pwd` `/etc/passwd` `Linux 파일 계층` `Root(/)` `Home(~)` `절대 경로` `상대 경로` `mkdir` `rmdir` | 터미널과 셸의 차이, 셸 스크립트 기초, Linux 파일 시스템 구조, 디렉터리 이동·생성·삭제, 경로 개념 |

---

## 🗂️ 학습 영역 구분

### 01. Linux 기본 환경

**키워드**

`Ubuntu` · `Terminal` · `Shell` · `Bash` · `CLI`

- 터미널과 셸의 차이
- Bash 기본 개념
- 명령어 입력 및 실행
- Shell Script 기초

### 02. 파일 시스템

**키워드**

`/` · `/home` · `/root` · `/etc` · `/usr` · `/var` · `/tmp` · `mount`

- Linux 파일 계층 구조
- Root Directory
- Home Directory
- 시스템 주요 디렉터리
- 파일 종류
- 마운트 개념

### 03. 경로와 디렉터리 관리

**키워드**

`절대 경로` · `상대 경로` · `pwd` · `cd` · `mkdir` · `rmdir` · `~` · `.` · `..`

- 현재 작업 디렉터리 확인
- 절대 경로와 상대 경로
- 디렉터리 생성
- 디렉터리 삭제
- `mkdir -p`, `rmdir -p`

### 04. 서버 운영 기초

**학습 예정 키워드**

`사용자` · `권한` · `chmod` · `chown` · `sudo` · `process` · `systemd` · `service`

- Linux 사용자 및 그룹
- 파일 권한
- 프로세스 관리
- 서비스 관리

### 05. 네트워크 / 서버

**학습 예정 키워드**

`IP` · `Port` · `SSH` · `TCP/IP` · `DNS` · `curl` · `ping` · `netstat` · `ss`

- 네트워크 기본 개념
- SSH 원격 접속
- 포트와 프로세스
- 서버 네트워크 확인

### 06. 웹 서버 / 배포

**학습 예정 키워드**

`Nginx` · `Apache` · `Tomcat` · `Reverse Proxy` · `JAR` · `WAR` · `환경 변수`

- 웹 서버와 WAS
- Reverse Proxy
- Java 애플리케이션 실행
- Tomcat 배포
- 환경 변수 및 서버 설정

### 07. Git / 배포 자동화

**학습 예정 키워드**

`Git` · `GitHub` · `SSH Key` · `GitHub Actions` · `CI/CD` · `Deploy`

- 서버에서 Git 저장소 사용
- SSH 인증
- 빌드 및 배포 자동화
- CI/CD 기본 흐름

---

## 🚀 최종 배포 학습 흐름

```text
Linux 기초
   ↓
파일 / 디렉터리
   ↓
사용자 / 권한
   ↓
프로세스 / 서비스
   ↓
네트워크 / SSH
   ↓
웹 서버 / WAS
   ↓
Java 애플리케이션 실행
   ↓
Git 기반 배포
   ↓
CI/CD 자동 배포
```

---

## 📝 학습 기록 작성 규칙

날짜별 디렉터리를 생성하고 그 안에 해당 날짜의 학습 내용을 기록합니다.

```text
ubuntu-study/
├── README.md
├── 2026-09-11/
│   ├── linux.md
│   ├── hello
│   └── hello1
└── 다음 학습 날짜/
    └── 학습내용.md
```

### 날짜별 문서 작성 원칙

1. **개념** — 무엇인지 설명
2. **키워드** — 핵심 명령어와 개념을 정리
3. **실습** — 실제 Ubuntu에서 실행한 명령어 기록
4. **결과** — 실행 결과 및 확인 내용
5. **핵심 정리** — 복습할 내용 요약

---

## 🎯 최종 목표

Linux 서버 환경에서 다음 과정을 직접 수행할 수 있도록 학습합니다.

```text
GitHub Repository
      ↓
Linux Server
      ↓
Git Clone / Pull
      ↓
Build
      ↓
Application 실행
      ↓
Web Server / WAS 연결
      ↓
외부 접속
```

**핵심 목표:** 단순히 Linux 명령어를 암기하는 것이 아니라, 실제 서버에서 애플리케이션을 **실행하고 관리하고 배포하는 능력**을 갖추는 것.
