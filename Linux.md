# Linux 기초 및 WSL2 Ubuntu 환경 구축

## 목차

- [1주차](#1주차)
  - [1. Linux란 무엇인가?](#1-linux란-무엇인가)
  - [2. WSL2란 무엇인가?](#2-wsl2란-무엇인가)
  - [3. Linux 파일 시스템 심화](#3-linux-파일-시스템-심화)
  - [4. 사용자·그룹·권한 관리](#사용자그룹권한-관리)
  - [5. 파일과 디렉터리의 권한 차이](#파일과-디렉터리의-권한-차이)
  - [6. `chmod`, `chown`, `chgrp`의 차이](#chmod-chown-chgrp의-차이)
  - [7. APT 패키지 관리와 Git 기초](#apt-패키지-관리와-git-기초)
  - [8. Linux에서 프로그램을 설치하는 방식](#linux에서-프로그램을-설치하는-방식)
  - [9. `sudo`란](#sudo란)
  - [10. Git의 필요성](#10-git의-필요성)
  - [11. Git의 핵심 구조](#11-git의-핵심-구조)

- [2주차: Linux 프로세스 관리](#2주차-linux-프로세스-관리)
  - [1. Linux 프로세스와 PID](#1-linux-프로세스와-pid)
  - [2. PID와 PPID](#2-pid와-ppid)
  - [3. Foreground와 Background](#3-foreground와-background)
  - [4. 프로세스 상태와 종료 신호](#4-프로세스-상태와-종료-신호)
  - [5. 트러블슈팅 관점에서 중요한 연결](#5-트러블슈팅-관점에서-중요한-연결)

# 1주차

## 1. Linux란 무엇인가?

### 운영체제와 Linux 커널

- **운영체제(OS)**
  - CPU, 메모리, 저장 장치 등 하드웨어 자원 관리
  - 프로그램 실행 환경 제공
- **커널(Kernel)**
  - 운영체제의 핵심 구성 요소
  - 프로그램과 하드웨어 사이의 자원 사용 관리

### 주요 구성 요소

```text
사용자 프로그램 (Python, Nginx, Git 등)
          ↓
셸 (Shell, 예: Bash)
          ↓
Linux 커널 (프로세스, 메모리, 파일 시스템, 네트워크 관리)
          ↓
하드웨어 또는 가상 하드웨어 (CPU, 메모리, 디스크, 네트워크)
```

| 구성 요소 | 역할 |
| --- | --- |
| **Linux 커널** | CPU, 메모리, 프로세스, 장치 등 시스템 자원 관리 |
| **Ubuntu** | Linux 커널, 셸, 패키지 및 시스템 관리 도구 등을 제공하는 Linux 배포판 |
| **Bash** | 사용자 명령을 해석하고 실행할 피르고름 탐색 및 실행 요청하는 셸 |

### 파일 목록 확인 예시

- 현재 디렉터리의 파일 목록 확인
- Bash에 `ls` 명령 입력

```bash
ls
```

### 명령 실행 흐름

```text
사용자가 ls 입력 → Bash가 ls 프로그램 실행 → 커널이 파일 시스템 접근 처리 → 파일 목록 출력
```

- 프로그램이 커널에 특정 작업을 요청할 때, **시스템 콜(System Call)**이란 인터페이스를 이용함
- **시스템 콜**은 일반 응용 프로그램이 운영체제(OS)의 핵심인 커널에게 위험하거나 중요한 작업(하드웨어 접근, 메모리 할당 등)을 대신 처리해 달라고 요청하는 창구 메서드

## 2. WSL2란 무엇인가?

- **WSL2(Windows Subsystem for Linux 2)**
  - Windows에서 Linux 환경을 사용할 수 있도록 지원
  - 경량 가상 머신에서 실제 Linux 커널 실행
  - Ubuntu 등 Linux 배포판의 사용자 공간 제공

### PowerShell에서 Ubuntu 실행

```powershell
# WSL의 기본 구성 상태 확인
wsl --status

# 설치된 배포판, 실행 상태, WSL 버전 확인
wsl --list --verbose

# Ubuntu 실행
wsl -d Ubuntu
```

### 배포판 상태 확인

`wsl --list --verbose` 출력 예시:

```text
  NAME      STATE      VERSION
* Ubuntu    Stopped    2
```

- `Stopped`: 배포판이 설치되어 있으나 현재 실행 중이지 않은 상태
- `Running`: 배포판이 실행 중인 상태
- `VERSION`의 `2`: 해당 배포판이 WSL2를 사용하는 상태

## 실습 - 파일과 디렉터리 생성

### Step 1. 운영체제와 계정 확인

```bash
whoami              # 현재 Linux 사용자 확인
pwd                 # 현재 작업 디렉터리 확인
uname -r            # Linux 커널 버전 확인
cat /etc/os-release # Ubuntu 배포판 정보 확인
```

### Step 2. Linux 디렉터리 구조 이해

```text
/       최상위 루트 디렉터리
├── home  일반 사용자 홈 디렉터리
├── etc   시스템 및 프로그램 설정 파일
├── var   로그와 가변 데이터
├── usr   사용자 공간 프로그램 및 라이브러리
├── tmp   임시 파일
└── mnt   외부 파일 시스템 마운트 위치
```

- `/home/user`
  - `user` 계정의 홈 디렉터리 예시
  - 실제 경로는 사용자 이름에 따라 달라짐
- `/mnt/c/Users/...`
  - WSL에서 Windows C 드라이브에 접근할 때 사용하는 경로

### Step 3. 실습용 디렉터리 생성

```bash
# 디렉터리 생성 (-p는 필요한 상위 디렉터리도 함께 생성)
mkdir -p ~/cloud-lab/week01

# 생성한 디렉터리로 이동 (~는 현재 사용자의 홈 디렉터리)
cd ~/cloud-lab/week01

# 현재 위치 확인
pwd
```

예상 출력:

```text
/home/user/cloud-lab/week01
```

- `user` 부분은 실제 Linux 사용자 이름으로 표시

### Step 4. 파일 생성과 조회

```bash
# 빈 파일을 만듦 (파일이 이미 있으면 접근·수정 시각을 갱신)
touch notes.txt

# 문자열을 파일에 기록 (파일이 이미 있으면 내용을 덮어씀)
printf 'My first Linux lab\n' > notes.txt

# 파일 목록을 자세히 표시
ls -l

# 파일 내용 확인
cat notes.txt
```

`ls -l` 출력 예시:

```text
-rw-r--r-- 1 user user 19 Nov  2 23:49 notes.txt
```

- 첫 번째 항목인 `-rw-r--r--`
  - 파일 종류와 접근 권한 표시
  - 파일 권한은 `644`
- 사용자 이름, 날짜 등
  - 실행 환경에 따라 다르게 표시

| 구분 | 실제 값 | 의미 |
| --- | --- | --- |
| 파일 종류 | - | 일반 파일 |
| 소유자 권한 | rw- | 읽기·쓰기 가능 |
| 그룹 권한 | r-- | 읽기 가능 |
| 기타 사용자 권한 | r-- | 읽기 가능 |
| 소유자 | user | 파일 소유 계정 |
| 그룹 | user | 파일 소유 그룹 |
| 파일 크기 | 19 | 19바이트 |

### Step 5. 파일 권한 확인 및 복구

- `chmod 000`으로 모든 사용자의 파일 접근 권한 제거
- 소유자, 그룹, 그 외 사용자 모두 읽기·쓰기·실행 불가

```bash
# 모든 사용자에게 권한을 주지 않음
chmod 000 notes.txt

# 일반 사용자로 읽기 시도
cat notes.txt
```

예상 오류:

```text
cat: notes.txt: Permission denied
```

- 실습 후 `chmod 600`으로 권한 복구
- `600`: 소유자에게만 읽기(`4`) 및 쓰기(`2`) 권한 부여

```bash
chmod 600 notes.txt
cat notes.txt
```

## 3. Linux 파일 시스템 심화

### Linux 경로를 표현하는 네 가지 기호

| 기호 | 의미 | 예시 |
| --- | --- | --- |
| `/` | 최상위 디렉터리 | `/etc`, `/home` |
| `~` | 현재 사용자의 홈 디렉터리 | `~/cloud-lab` |
| `.` | 현재 작업 디렉터리 | `./test.txt` |
| `..` | 부모 디렉터리 | `../notes.txt` |

```bash
# 실습 디렉터리로 이동
cd ~/cloud-lab/week01/practice

# 현재 위치와 디렉터리 내용 확인
pwd
ls .    # 현재 디렉터리의 내용
ls ..   # 부모 디렉터리의 내용

# 부모 디렉터리로 이동한 뒤 현재 위치 확인
cd ..
pwd
```

### Linux 파일 관리 명령어

| 명령어 | 기능 | 예시 |
| --- | --- | --- |
| `cp` | 파일 복사 | `cp a.txt b.txt` |
| `mv` | 파일 이동 또는 이름 변경 | `mv a.txt b.txt` |
| `rm` | 파일 삭제 | `rm a.txt` |
| `find` | 조건에 맞는 파일 검색 | `find . -name "*.txt"` |

- `mv`
  - 파일을 다른 위치로 이동하거나 이름을 변경할 때 사용
- `cp`
  - 원본 파일을 유지하면서 지정한 위치에 복사본 생성
  - 대상 파일이 이미 있으면 덮어쓸 수 있음

## 실습 - 파일 관리 및 장애 진단

### Step 1. 작업 공간 만들기

```bash
# 실습 디렉터리 생성 및 이동
mkdir -p ~/cloud-lab/week01/lesson02
cd ~/cloud-lab/week01/lesson02

# 현재 디렉터리에 docs와 backup 디렉터리 생성
mkdir docs backup

# docs 디렉터리에 파일 생성
printf 'Linux server configuration\n' > docs/config.txt
```

### 생성된 디렉터리 구조

```text
lesson02/
├── docs/
│   └── config.txt
└── backup/
```

### Step 2. 복사와 이름 변경

```bash
# config.txt를 backup 디렉터리에 복사
cp docs/config.txt backup/config.txt

# 원본 파일 이름 변경
mv docs/config.txt docs/settings.txt

# 현재 디렉터리부터 하위 경로에서 일반 파일 검색
find . -type f
```

### Step 3. 오류 발생과 진단

```bash
cat docs/config.txt
```

- `mv` 명령으로 파일 이름을 `settings.txt`로 변경했기 때문에 `docs/config.txt`는 더 이상 존재하지 않음
- 따라서 위 명령을 실행하면 파일을 찾을 수 없다는 오류가 발생

```bash
# 변경된 파일 이름으로 내용 확인
cat docs/settings.txt
```

- 변경된 파일 이름을 사용하면 파일 내용 확인 가능

### find 명령어

- 현재 디렉터리부터 하위 디렉터리까지 `.txt`로 끝나는 일반 파일 검색

```bash
find . -type f -name "*.txt"
```

| 구성 요소 | 의미 |
| --- | --- |
| `find` | 파일 및 디렉터리 검색 명령어 |
| `.` | 현재 디렉터리를 검색 시작점으로 지정 |
| `-type f` | 일반 파일만 검색 |
| `-name "*.txt"` | 이름이 `.txt`로 끝나는 파일 검색 |

- `*`는 임의의 문자열을 나타내는 와일드카드
- 따옴표로 감싸면 셸이 `*`를 먼저 확장하지 않고 `find`에 패턴을 전달

## 4. 사용자·그룹·권한 관리

### Linux의 사용자 권한 구분

- Linux는 사용자와 프로그램마다 필요한 권한만 부여해 시스템을 보호함
- 웹 서버 실행 환경 예시
  - 관리자: 서버 설정 변경 및 프로그램 설치
  - 개발자: 애플리케이션 코드 수정
  - 웹 서버 프로세스: 필요한 웹 파일 읽기 및 지정 위치에 데이터 기록
- 모든 사용자와 프로그램이 관리자 권한을 가지면, 프로그램 하나의 문제가 시스템 전체에 영향을 줄 수 있음
- 파일마다 소유자, 그룹, 기타 사용자별 접근 권한을 분리해 위험을 줄임

### Linux 파일 권한 구조

- 권한은 소유자, 그룹, 기타 사용자별로 설정
- 각 범위의 권한은 읽기(`r`), 쓰기(`w`), 실행(`x`)의 조합으로 표현

| 권한 | 숫자 |
| --- | --- |
| 읽기(`r`) | `4` |
| 쓰기(`w`) | `2` |
| 실행(`x`) | `1` |
| 권한 없음(`-`) | `0` |

`chmod 750` 설정 예시:

| 대상 | 계산 | 권한 |
| --- | --- | --- |
| 소유자 | `7 = 4 + 2 + 1` | `rwx` |
| 그룹 | `5 = 4 + 1` | `r-x` |
| 기타 사용자 | `0` | `---` |

- 전체 권한 표기: `rwxr-x---`
- 파일과 디렉터리에서 `rwx`가 의미하는 바는 서로 다름
- 디렉터리의 `x`는 해당 경로로 이동하거나 알고 있는 이름을 통해 내부 항목에 접근할 수 있는 권한

## 실습 - 사용자와 그룹 확인

```bash
whoami    # 현재 사용자 이름 확인
id        # UID, 기본 GID, 소속 그룹 확인
groups    # 사용자가 속한 그룹 확인
id -u     # 현재 사용자의 UID만 출력
id -gn    # 현재 사용자의 기본 그룹 이름 출력
```

`id` 출력 예시:

```text
uid=1000(user) gid=1000(user) groups=1000(user),27(sudo)
```

| 항목 | 설명 |
| --- | --- |
| `uid=1000(user)` | 사용자 ID와 사용자 이름 |
| `gid=1000(user)` | 기본 그룹의 ID와 그룹 이름 |
| `groups=...` | 사용자가 속한 기본 그룹 및 보조 그룹 목록 |

대표적인 그룹 예시:

| 그룹 | 일반적인 용도 |
| --- | --- |
| `adm` | 시스템 로그에 대한 접근 권한 제공 |
| `cdrom` | CD/DVD-ROM 장치 접근 권한 제공 |
| `sudo` | `sudo`를 사용할 수 있는 권한 제공 |
| `dip` | 네트워크 연결 관리와 관련된 권한 제공 |
| `plugdev` | 일부 환경에서 외부 장치 접근 권한 제공 |
| `users` | 일반 사용자 그룹 |

- 그룹 이름과 권한은 배포판 및 시스템 설정에 따라 다를 수 있음

## Linux 사용자와 그룹

```text
사용자: user (예시 UID: 1000)
소속 그룹: user, sudo 등 (실제 그룹은 시스템마다 다름)
```

### UID와 GID가 필요한 이유

- Linux 커널은 프로세스의 사용자·그룹 식별 정보와 파일의 소유권·접근 권한을 비교함
- 권한 확인 순서
  1. 프로세스가 파일 소유자라면 소유자 권한 확인
  2. 소유자가 아니고 파일 소유 그룹에 속한다면 그룹 권한 확인
  3. 둘 다 해당하지 않으면 기타 사용자 권한 확인
- 세 범위의 권한을 합산하지 않고, 사용자와 그룹 관계에 따라 해당하는 한 범위의 권한을 적용

## 5. 파일과 디렉터리의 권한 차이

- `rwx`의 의미는 일반 파일과 디렉터리에서 다름

| 권한 | 일반 파일 | 디렉터리 |
| --- | --- | --- |
| `r`(Read) | 파일 내용 읽기 | 내부 항목 이름 조회 |
| `w`(Write) | 파일 내용 수정 | 내부 항목 생성·삭제·이름 변경 |
| `x`(Execute) | 실행 가능한 파일 실행 | 디렉터리 진입·경로 탐색 |

- 디렉터리 안에 항목을 만들거나 삭제·이름 변경하려면 일반적으로 디렉터리에 `w`와 `x` 권한이 모두 필요함

## 실습 - 파일 권한 변경과 오류 복구

```bash
mkdir -p ~/cloud-lab/week01/permissions
cd ~/cloud-lab/week01/permissions

# 실습 파일 생성
printf 'Linux permissions practice\n' > sample.txt

# 파일 권한 확인
ls -l sample.txt
```

#### `umask`란?

- 새 파일이나 디렉터리를 만들 때 기본 권한에서 제거할 권한을 지정하는 값
- 일반적인 기본 권한
  - 디렉터리: `777` (`rwxrwxrwx`)
  - 일반 파일: `666` (`rw-rw-rw-`)
- `umask 022` 예시
  - 디렉터리: `755` (`rwxr-xr-x`)
  - 일반 파일: `644` (`rw-r--r--`)
- `umask` 명령어로 현재 설정 확인 가능
- 실제 기본 권한은 배포판, 셸, 계정 설정에 따라 달라질 수 있음

- 일반 파일에 기본 실행 권한을 주지 않는 이유
  - 새 파일이 의도치 않게 실행 가능한 상태가 되는 것을 방지
  - 실행 권한은 필요한 파일에 별도로 부여

```bash
# 소유자에게 읽기 권한만 부여
chmod 400 sample.txt

# 쓰기 권한이 없어 추가 작업이 실패
printf 'New line\n' >> sample.txt

# 소유자에게 읽기·쓰기 권한, 그룹에는 읽기 권한 부여
chmod 640 sample.txt
printf 'New line\n' >> sample.txt
cat sample.txt
```

- `>>`는 기존 파일의 끝에 내용을 추가하는 리다이렉션 연산자
- `400`에서는 소유자에게 쓰기 권한이 없어 내용 추가가 실패
- `640`으로 변경한 뒤에는 소유자가 내용을 추가할 수 있음

## 실습 - 디렉터리 실행 권한의 역할

### Step 1. 디렉터리 생성

```bash
cd ~/cloud-lab/week01/permissions
mkdir private
chmod 700 private
ls -ld private
```

- `private` 디렉터리 자체의 권한 확인
- `-ld` 옵션: 디렉터리 내부가 아니라 디렉터리 자체의 상세 정보 표시
- 설정한 권한: `drwx------` (소유자에게만 읽기·쓰기·탐색 허용)

### Step 2. 디렉터리 탐색 권한 제거

```bash
chmod 600 private
ls -ld private
cd private
```

예상 오류:

```text
-bash: cd: private: Permission denied
```

- `private` 디렉터리에 읽기·쓰기 권한은 있지만 `x` 권한이 없어 내부로 진입할 수 없음

### Step 3. 권한 복구

```bash
chmod 700 private
cd private
pwd
```

- 현재 경로가 `/home/user/cloud-lab/week01/permissions/private`와 같이 출력됨

## 6. chmod, chown, chgrp의 차이

| 명령어 | 역할 | 예시 |
| --- | --- | --- |
| `chmod` | 파일 접근 권한 변경 | `chmod 640 file.txt` |
| `chown` | 파일 소유자 변경 | `chown alice file.txt` |
| `chgrp` | 파일 소유 그룹 변경 | `chgrp developers file.txt` |

- 파일에 실행 권한 `x`가 있어도 모든 파일을 실행할 수 있는 것은 아님
  - 실행 권한은 실행에 필요한 조건 중 하나
  - 파일 형식, 인터프리터, 경로 접근 권한 등도 영향을 줌

```bash
# 파일 자체의 실행 권한이 필요한 경우
./script.sh

# Bash가 파일을 읽어 해석하므로 파일 자체의 실행 권한이 필요 없음
bash script.sh
```

- `chmod 640`만으로 웹 서버가 반드시 파일을 읽을 수 있는 것은 아님
  - 웹 서버 프로세스가 파일 소유자이거나 파일의 소유 그룹에 속해야 함
  - 파일까지 이어지는 상위 디렉터리에 필요한 탐색(`x`) 권한이 있어야 함

## 7. APT 패키지 관리와 Git 기초

- **APT**
  - Debian 및 Ubuntu 계열에서 패키지를 설치·업데이트·삭제하는 패키지 관리자
  - 저장소에서 패키지를 검색하고 다운로드
  - 필요한 의존성을 확인하고 함께 설치
  - 설치된 패키지를 업그레이드
- **Git**
  - 파일과 코드의 변경 이력을 관리하고 이전 상태를 추적하는 버전 관리 시스템

## 8. Linux에서 프로그램을 설치하는 방식

- Windows에서 `.exe` 또는 `.msi` 설치 파일을 사용하는 것처럼, Linux에서는 APT로 패키지를 설치할 수 있음

### 설치 흐름

```text
사용자: sudo apt install git
              ↓
APT: 패키지 목록·버전·의존성 확인
              ↕
저장소: 설정된 저장소에서 패키지 확보
              ↓
설치: APT가 dpkg를 이용해 패키지 설치
```

- `apt`는 로컬 패키지 목록을 참고해 필요한 패키지와 의존성을 계산
- 필요한 패키지가 로컬에 없으면 설정된 저장소에서 다운로드
- 저장소 설정은 `/etc/apt/sources.list` 또는 `/etc/apt/sources.list.d/` 아래에서 관리
- APT는 `dpkg`를 이용해 패키지를 설치하고 패키지 데이터베이스를 관리

### APT의 주요 명령어

| 명령어 | 역할 |
| --- | --- |
| `sudo apt update` | 저장소에서 패키지 목록을 새로 받아옴 |
| `sudo apt upgrade` | 설치된 패키지를 업그레이드 |
| `sudo apt install git` | Git 설치 |
| `sudo apt remove git` | Git 패키지 제거 |
| `apt show git` | Git 패키지 정보 확인 |
| `apt list --installed` | 설치된 패키지 목록 확인 |

#### `apt update`와 `apt upgrade`의 차이

- `apt update`
  - 저장소의 최신 패키지 목록을 내려받아 갱신
  - 설치된 프로그램 자체를 업그레이드하지는 않음
- `apt upgrade`
  - 갱신된 목록을 바탕으로 설치된 패키지를 실제로 업그레이드

## 9. sudo란

```bash
sudo apt install git
```

| 구성 요소 | 의미 |
| --- | --- |
| `sudo` | 허용된 명령을 다른 사용자(기본적으로 `root`) 권한으로 실행 |
| `apt` | Ubuntu의 패키지 관리 도구 |
| `install` | 설치 작업 |
| `git` | 설치할 패키지 이름 |

- `sudo`는 현재 계정을 영구적으로 `root`로 전환하지 않음
- 지정한 명령만 관리자 권한으로 실행
- 일반 작업은 일반 사용자 권한으로 수행하고, 시스템 변경이 필요할 때만 관리자 권한

### 모든 명령에 `sudo`를 붙이지 않는 이유

- 필요한 권한보다 높은 권한으로 실행하면 실수의 영향 범위가 커짐
- 관리자 권한으로 만든 파일은 `root` 소유가 될 수 있음
- 이후 일반 사용자로 수정할 때 권한 오류가 발생할 수 있음

```bash
# 관리자 권한으로 파일 생성 예시
sudo touch ~/cloud-lab/test.txt
```

- 일반 사용자 권한으로 가능한 작업에는 `sudo`를 사용하지 않는 것이 원칙

## 실습 - APT로 Git 설치

### Step 1. APT와 Git 설치 상태 확인

```bash
apt --version
git --version
```

### Step 2. 패키지 목록 갱신

```bash
sudo apt update
```

### Step 3. Git 설치

```bash
sudo apt install git

git --version

# Git 패키지의 메타데이터 조회
apt show git
```

#### 오류 메시지와 확인 항목

| 오류 | 원인 | 우선 확인 |
| --- | --- | --- |
| `Permission denied` | 관리자 권한 부족 | `sudo` 사용 여부 |
| `Unable to locate package` | 패키지 목록 또는 저장소 문제 | `sudo apt update` 결과 |
| `Temporary failure resolving` | DNS·네트워크 문제 | WSL 네트워크 연결 |
| `Could not get lock` | 다른 패키지 관리 작업이 진행 중 | 다른 APT 프로세스 유무 |

## 10. Git의 필요성

- AWS 서버를 구축할 때 다음과 같은 파일을 관리할 수 있음

```text
cloud-infrastructure/
├── README.md
├── terraform/
│   ├── main.tf
│   └── variables.tf
├── scripts/
│   └── deploy.sh
└── nginx/
    └── default.conf
```

- 서버 설정 변경 후 장애가 발생하면 원인이 된 변경 사항을 확인할 필요가 있음
- Git은 파일 변경 이력을 기록하는 버전 관리 시스템
- Git으로 할 수 있는 작업
  - 변경된 파일과 내용을 확인
  - 변경 이력을 기록하고 이전 버전과 비교
  - 필요할 때 이전 상태로 복구
  - GitHub 등의 원격 저장소를 통해 협업

## 11. Git의 핵심 구조

- **Working Directory(작업 디렉터리)**: 실제 파일을 수정하는 공간
- **Staging Area(스테이징 영역)**: 다음 커밋에 포함할 변경 사항을 선택해 두는 공간
- **Repository(저장소)**: 커밋 이력을 저장하는 공간

- `git add`: 변경 내용을 다음 커밋에 포함하도록 스테이징
- `git commit`: 스테이징된 내용을 저장소의 변경 이력으로 기록

## 실습 - Git 저장소 구축

### Step 1. 실습 디렉터리 생성

```bash
mkdir ~/cloud-lab/week01/git-lab
cd ~/cloud-lab/week01/git-lab

# Git 저장소 생성 및 초기 브랜치 main으로 명명
git init -b main
```

- 저장소 생성 과정에서 `.git` 숨김 디렉터리가 생성됨
- `ls -la` 명령으로 확인 가능
- `-b`는 초기 브랜치 이름을 지정하는 옵션

### Step 2. Git 사용자 정보 설정

```bash
# 현재 저장소의 커밋 작성자 이름 설정
git config --local user.name "Linux Learner"

# 현재 저장소의 커밋 작성자 이메일 설정
git config --local user.email "learner@example.invalid"

# 현재 저장소의 설정을 확인
git config --local --list
```

- `--local`: 현재 저장소에만 설정 적용
- `--global`: 현재 사용자 계정의 모든 저장소에 설정 적용

### Step 3. 파일 생성

```bash
printf '# Cloud Linux Lab\n' > README.md
git status
```

- `git status`로 파일이 추적되지 않는 상태인지 확인

### Step 4. 오류 재현 및 해결

- `git add` 없이 커밋을 시도해 스테이징되지 않은 변경 사항은 커밋에 포함되지 않는 것을 확인

```bash
git commit -m "Initial Linux lab"
```

- 파일을 스테이징한 뒤 커밋

```bash
git add README.md
git commit -m "Initial Linux lab"

# 변경 이력 확인
git log --oneline
```

- `git log --oneline`으로 커밋 이력 확인

### Step 5. 변경 후 내용 비교

```bash
printf 'Week 1: Linux basic\n' >> README.md
git status

# 작업 디렉터리와 스테이징 되지 않은 변경 사항 비교
git diff

# 변경 사항을 스테이징하고 스테이징된 내용 확인
git add README.md
git diff --cached

# 커밋 후 이력 확인
git commit -m "Update Linux study notes"
git log --oneline
```

# 2주차: Linux 프로세스 관리

## 1. Linux 프로세스와 PID

### 프로그램과 프로세스

- **프로그램(Program)**: 디스크에 저장된 실행 코드
- **프로세스(Process)**: 실행 중인 프로그램의 인스턴스이자 운영체제가 관리하는 실행 단위
- `python3`를 실행하지 않은 상태에서는 프로그램 파일만 존재
- 프로그램을 실행하면 Linux 커널이 프로세스의 메모리, CPU 실행 상태, 열린 파일 등의 자원을 관리

```text
프로그램: /usr/bin/python3 - 디스크에 저장된 실행 파일
            ↓     프로그램 실행     ↓
        프로세스 A             프로세스 B
(하나의 프로그램으로 여러 프로세스를 실행할 수 있음)
```

- **핵심**: 하나의 프로그램을 여러 번 실행하면 서로 다른 프로세스가 만들어질 수 있음

#### 프로세스가 사용하는 자원

| 자원·정보 | 역할 |
| --- | --- |
| `PID` | 프로세스 식별 번호 |
| 메모리 | 코드, 데이터, 스택, 힙 등 |
| CPU 실행 상태 | 어떤 코드를 어디까지 실행했는지 관리 |
| 파일 디스크립터 | 열린 파일, 소켓 등 참조 |
| 프로세스 상태 | 실행 가능, 대기, 종료 등 |
| 사용자·그룹 정보 | 접근 권한 판단에 사용 |

- **파일 디스크립터(File Descriptor, FD)**
  - 프로세스가 연 파일이나 입출력 대상을 가리키는 정수 번호
  - 파일뿐 아니라 터미널, 파이프, 소켓 등을 가리킬 수 있음
  - 프로그램은 FD를 사용해 커널에 읽기·쓰기·닫기 작업을 요청

## 2. PID와 PPID

- **PID(Process ID)**: 프로세스 식별 번호
- **PPID(Parent Process ID)**: 부모 프로세스의 식별 번호

### 부모 프로세스와 자식 프로세스

```text
Bash 프로세스 (PID = 1500)
    ↓ 자식 프로세스 생성 및 실행
sleep 프로세스 (PID = 1501)
```

- 예시에서 `sleep`의 PID는 `1501`, PPID는 부모 Bash의 PID인 `1500`

### 프로세스 생성 과정

- 일반적인 외부 프로그램 실행 과정
  1. `fork()` 또는 관련 시스템 호출로 자식 프로세스 생성
  2. 자식 프로세스에서 `execve()` 계열 호출로 실행 프로그램 교체
  3. 커널 스케줄러가 실행 가능한 프로세스에 CPU 시간 배분
  4. 프로세스 종료 후 부모 프로세스가 `wait()` 계열 호출로 종료 상태 회수
- 프로세스의 생성부터 종료까지 운영체제가 관리

### exec

- Bash의 `exec` 내장 명령은 새 프로세스를 만들지 않고 현재 셸 프로세스를 지정한 프로그램으로 대체
- 일반적으로 프로그램 실행 파일은 `execve()` 계열 시스템 호출로 현재 프로세스의 프로그램 이미지를 교체
- 주요 특징
  - 프로세스 ID(PID)는 유지
  - 대체가 성공하면 기존 셸은 사라지고 프로그램이 같은 프로세스에서 실행
  - 프로그램이 종료되어도 대체된 셸로 돌아오지 않음
  - `exec` 뒤에 작성한 셸 명령은 실행되지 않음
  - 실행이 실패하면 기존 셸은 보통 계속 실행 가능
- 사용 예시
  - 스크립트의 마지막에 주 프로그램을 실행해 불필요한 셸 프로세스가 남지 않게 함
  - 컨테이너에서 주 프로그램이 해당 프로세스에 전달되는 신호를 직접 받도록 실행 구조를 단순화
- `exec`는 자원 제한을 복제하지 않게 하거나 환경 변수 유실을 막는 명령이 아님
  - 자식 프로세스도 부모로부터 환경과 여러 실행 속성을 일반적인 규칙에 따라 상속
  - `export`되지 않은 셸 변수는 실행 프로그램의 환경 변수로 전달되지 않음
- 신호 전달 동작은 실행 환경과 프로세스 구조에 따름
  - `exec`로 셸을 대체하면 주 프로그램이 해당 프로세스에 전달되는 신호를 직접 받을 수 있음
  - 모든 상황에서 신호 전달이 자동으로 보장된다는 뜻은 아님

---

## 실습 - Bash 프로세스 확인

### Step 1. 현재 Bash의 PID 확인

```bash
mkdir -p ~/cloud-lab/week02/lesson01
cd ~/cloud-lab/week02/lesson01

# 현재 Bash 셸의 PID 출력
echo $$

# PID, 부모 PID, 상태, 명령 정보 확인
ps -p "$$" -o pid,ppid,stat,comm,args
```

| 옵션·필드 | 역할 |
| --- | --- |
| `ps` | 프로세스 정보 조회 |
| `-p` | 지정한 PID의 프로세스 선택 |
| `-o` | 출력할 필드 지정 |
| `pid` | 프로세스 ID |
| `ppid` | 부모 프로세스 ID |
| `stat` | 프로세스 상태 |
| `comm` | 실행 프로그램 이름 |
| `args` | 실행 명령과 인수 |

```text
  PID  PPID STAT COMMAND COMMAND
  330   326 Ss   bash    -bash
```

## 3. Foreground와 Background

### Foreground

- **Foreground(포그라운드) 작업**
  - 현재 터미널과 연결되어 실행되는 작업
  - 작업이 끝날 때까지 셸 프롬프트를 사용할 수 없음

```bash
sleep 5
```

- 대화형 Bash에서 위 명령을 실행하면 5초 후에 프롬프트가 다시 표시됨
- 예시: `vim index.js`, `ls`, `git pull`, `tail -f`
- 서버처럼 계속 실행되는 작업을 포그라운드에서 실행하면 작업이 끝나거나 중단될 때까지 해당 셸에서 다른 명령을 입력할 수 없음

### Background

- **Background(백그라운드) 작업**
  - 셸 프롬프트를 돌려받은 상태에서 함께 실행되는 작업
  - 터미널을 계속 사용할 수 있지만, 작업이 CPU를 사용하지 않는다는 뜻은 아님

```bash
# 명령어 뒤에 &를 붙여 백그라운드에서 실행
sleep 100 &
```

- Bash는 작업을 시작한 뒤 프롬프트를 다시 표시
- 예시: 개발용 서버, 대용량 압축, 데이터베이스 백업

### 스위칭 상황별 예시

- 포그라운드 작업을 백그라운드로 전환
  - `Ctrl + Z`로 작업을 일시 중지
  - `bg`로 작업을 백그라운드에서 재개
- 백그라운드 작업을 포그라운드로 전환
  - `fg`로 작업을 포그라운드로 가져옴
  - 포그라운드 작업은 `Ctrl + C`로 중단 가능
- 장시간 실행되는 서비스는 보통 `systemd` 같은 서비스 관리 도구로 관리
  - 로그는 서비스 관리자나 애플리케이션 전용 명령으로 확인

## 실습 - 프로세스 직접 생성 및 관찰

### Step 1. 백그라운드 프로세스 생성

```bash
# 명령어 백그라운드 실행
sleep 180 &

# 가장 최근 백그라운드 작업의 PID를 변수에 저장
LAB_PID=$!

echo "Process PID: $LAB_PID"
```

### Step 2. 실행 중인 프로세스 관찰

```bash
ps -p "$LAB_PID" -o pid,ppid,stat,etime,comm
```

```text
  PID    PPID STAT     ELAPSED COMMAND
  607     336 S          00:04 sleep
```

- `PID 607`: `sleep` 프로세스의 ID
- `PPID 336`: 부모 Bash의 ID (예시 값은 실행 환경에 따라 다름)
- `S`: 대기(Sleeping) 상태
- `ELAPSED`: 프로세스 시작 후 경과 시간

- `sleep`은 실행 중인 프로세스지만 대부분의 시간 동안 대기 상태
- **프로세스가 존재한다는 것이 CPU를 계속 사용한다는 뜻은 아님**

### Step 3. jobs 명령어

```bash
# 현재 Bash 셸이 관리하는 작업(Job) 출력
jobs -l
```

```text
[1]+  607 Running   sleep 180 &
```

| 명령어 | 확인 대상 |
| --- | --- |
| `ps` | 운영체제가 관리하는 프로세스 |
| `jobs` | 현재 셸의 작업 테이블에 있는 작업 |

- `jobs`는 시스템 전체가 아니라 현재 셸이 관리하는 작업을 표시

## 4. 프로세스 상태와 종료 신호

| 상태 | 의미 |
| --- | --- |
| `R` | 실행 중 또는 CPU 실행 대기 |
| `S` | 인터럽트 가능한 대기 |
| `D` | 일반적인 신호로 중단하기 어려운 대기 |
| `T` | 중지 또는 추적 상태 |
| `Z` | 종료되었지만 아직 결과가 회수되지 않은 좀비 상태 |

- `ps`의 `STAT` 열에는 `S+`, `Ss`처럼 기본 상태와 부가 상태가 함께 표시될 수 있음

### 프로세스 종료하는 방법

- Linux에서는 프로세스에 신호(Signal)를 보내 특정 동작을 요청

| 신호 | 번호 | 의미 |
| --- | --- | --- |
| `SIGINT` | 2 | 터미널의 Ctrl + C 등으로 발생하는 인터럽트 |
| `SIGTERM` | 15 | 정상적인 종료를 요청하는 신호(무시할 수도 있음) |
| `SIGKILL` | 9 | 커널이 프로세스를 강제로 종료 |
| `SIGSTOP` | 19 | 프로세스 실행 중지 |
| `SIGCONT` | 18 | 중지된 프로세스 재개 |

- 일반적으로 먼저 `SIGTERM`으로 정상 종료를 요청

```bash
kill -TERM <PID>
```

- `SIGKILL`은 프로세스가 정리 작업을 수행할 기회를 주지 않으므로 신중하게 사용

## 실습 - 프로세스 종료 및 장애 진단

### Step 1. 정상 종료 요청

```bash
# SIGTERM 신호 전송
kill -TERM "$LAB_PID"
```

### Step 2. 종료 상태 확인

```bash
# 종료 상태(exit status) 회수
wait "$LAB_PID"

# 직전에 실행된 명령어의 종료 상태($?) 확인 
echo "Exit status: $?"
```

```text
Exit status: 143
```

- 143인 이유
  - Bash 셸이 종료 상태를 표현하는 번호 `128`에 `SIGTERM` 종료 번호인 15을 더함
  - `128 + 15 = 143`

### Step 3. 종료된 프로세스 확인

```bash
ps -p "$LAB_PID"

# kill -0은 실제 종료 신호를 보내지 않고 프로세스의 존재와 신호 전송 권한 검사
kill -0 "$LAB_PID"
```

```text
-bash: kill: (888) - No such process
```

- 프로세스가 이미 종료되어 해당 PID가 존재하지 않기 때문에 발생하는 오류

### Step 4. 새 프로세스 실행

```bash
sleep 180 &
LAB_PID=$!

ps -p "$LAB_PID" -o pid,ppid,stat,comm

kill -TERM "$LAB_PID"
wait "$LAB_PID"
```

## 5. 트러블슈팅 관점에서 중요한 연결

- AWS EC2에서 서버가 응답하지 않을 때, 프로세스 상태만으로 서비스 정상 여부를 판단할 수 없음
- 프로세스 상태와 애플리케이션 응답을 함께 확인

| 현상 | 가능한 상황 |
| --- | --- |
| PID가 조회되지 않음 | 프로세스가 종료되었거나 아직 시작되지 않음 |
| PID가 존재하지만 서비스 응답 없음 | 대기, 교착, 네트워크, 애플리케이션 문제 등 |
| 프로세스가 반복 종료 | 실행 오류, 설정 문제, 메모리 부족 등 |
| 재시작할 때마다 PID 변경 | 새 프로세스를 생성한 일반적인 결과 |

- **프로세스가 실행 중이어도 서비스가 정상이라는 보장은 없음**

## Linux 환경변수와 PATH

AWS EC2에서 서버 실행 시, 필요한 설정
 - 서버가 사용할 포트 번호
 - 개발 환경인지 운영 환경인지 구분하는 값
 - 외부 서비스의 접속 주소
 - 실행할 프로그램을 찾기 위한 경로

이러한 설정에 환경변수(Environment Variable)가 활용됨

### Shell 번수와 환경변수

```bash
# Bash 셸 변수 할당
APP_MODE=development
echo = "$APP_MODE"
```

Bash에서 변수를 만들었다고 해서 해당 변수의 값이 자식 프로세스에 자동으로 전달되지는 않음

#### 프로세스 간 변수 전달

```text 
                부모 Bash (APP_MODE=development)
                               │
             ┌─────────────────┴─────────────────┐
             │                                   │
           미설정                              export
             │                                   │
             ▼                                   ▼
      자식 프로세스 A                      자식 프로세스 B
       (변수 전달 X)                     (development 전달)
```

#### export의 역할

```bash
export APP_MODE=development
```

`export`는 Bash 변수를 자식 프로세스가 물려받을 환경에 포함하도록 표시하는 것  
이미 실행 중인 프로세스에 값을 전달하는 것이 아니라,  
**이후 실행하는 자식 프로세스가 해당 값을 상속**

| 구분 | 셸 변수 | 내보낸 환경변수(`export`한 변수) |
| --- | --- | --- |
| 현재 Bash에서 사용 | 가능 | 가능 |
| 새 외부 프로세스의 환경에 포함 | 기본적으로 불가 | 가능 |
| `export` 필요 | 아니요 | 예 |
| 부모 Bash 종료 후에도 자동 보존 | 아니요 | 아니요 |

일반 셸 변수는 Bash 내부에만 저장되므로, 외부 명령을 실행할 때 운영체제가 그 변수까지 환경으로 전달하지 않음  
`export`한 변수는 Bash가 외부 명령을 실행할 때 환경에 포함함

단, Bash 서브셸은 예외적으로 셸 변수의 복사본을 물려받음:

```bash
APP_MODE=development
(echo "$APP_MODE")  # development
```

특정 명령에만 전달하려면 `export` 대신 명령 앞에 변수를 지정할 수도 있음

```bash
APP_MODE=development some_command
```

## 실습 - 환경변수 상속 확인

### Step 1. 일반 Bash 변수 (셸 변수) 생성
```bash
mkdir -p ~/cloud-lab/week02/lesson02
cd ~/cloud-lab/week02/lesson02

LAB_MESSAGE=hello-cloud
echo "$LAB_MESSAGE"

# 자식 Bash 실행
bash -c 'echo "${LAB_MESSAGE:-not-set}"'
# bash -c는 전달받은 문자열을 새로운 Bash 프로세스에서 실행
```

`LAB_MESSAGE`가 부모 Bash에서만 정의되었고 아직 `export` 되지 않았기 때문에
그 결과 `not-set` 메세지 출력

### Step 2. export 적용

```bash
export LAB_MESSAGE

bash -c 'echo "${LAB_MESSAGE:-not-set}"'
```

이번엔 정상적으로 `hello-cloud` 출력

### Step 3. 환경변수 확인 및 정리

```bash
printenv LAB_MESSAGE
unset LAB_MESSAGE
```

`printenv`는 환경변수를 조회  
`unset`은 현재 셸에서 변수를 제거

## PATH 환경변수

```bash
ls
```

Bash가 `ls` 프로그램이 실제로 어디있는지 알 수 있는 이유
=> **PATH 환경변수**

PATH는 명령어 이름만 입력했을 때 실행 파일을 검색할 디렉터리 목록을 저장함