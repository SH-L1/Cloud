# Linux 기초 및 WSL2 Ubuntu 환경 구축

## 목차

- [1. Linux란 무엇인가?](#1-linux란-무엇인가)
- [2. WSL2란 무엇인가?](#2-wsl2란-무엇인가)
- [3. Linux 파일 시스템 심화](#3-linux-파일-시스템-심화)
- [4. 사용자·그룹·권한 관리](#사용자그룹권한-관리)
- [5. 파일과 디렉터리의 권한 차이](#파일과-디렉터리의-권한-차이)
- [6. `chmod`, `chown`, `chgrp`의 차이](#chmod-chown-chgrp의-차이)
- [7. APT 패키지 관리와 Git 기초](#apt-패키지-관리와-git-기초)
- [8. Linux에서 프로그램을 설치하는 방식](#linux에서-프로그램을-설치하는-방식)
- [9. `sudo`란](#sudo란)

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
- 저장소 설정은 `/etc/apt/sources.list` 또는 `/etc/apt/sources.list.d/` 아래에서 관리 (로컬 캐시에 저장)
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

### 왜 모든 명령을 sudo로 수행하면 안될까?

모든 명령에 `sudo`를 붙이게 되면 일반 사용자가 수행해도 되는 작업까지 관리자 권한으로 실행하게 됨

`sudo touch ~/cloud-lab/test.txt`

이렇게 명령을 실행하면 `test.txt`가 `root` 소유로 생성될 수 있음
그 결과, 일반 사용자 `user`로 수정하려고 할 때 권한 오류 발생

## 실습 - APT로 Git 설치

### Step 1. APT와 Gir 설치 상태 확인

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

#### 오류 메세지

| 오류 | 원인 | 우선 확인 |
| --- | --- | --- |
| `Permission denied` | 관리자 권한 부족 | `sudo` 사용 여부 |
| `Unable to locate package` | 패키지 목록 또는 저장소 문제 | `sudo apt update` 결과 |
| `Temporary failure resolving` | DNS·네트워크 문제 | WSL 네트워크 연결 |
| `Could not get lock` | 다른 패키지 관리 작업이 진행 중 | 다른 APT 프로세스 유무 |

## 10. Git의 필요성

AWS에 서버를 구축하면서 다음과 같은 파일을 만들게 됨

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

서버 설정 변경 후 장애 발생 시, 어떤 설정이 문제를 일으켰는지 알아야 함
Git은 파일 변경 이력을 기록하는 버전 관리 시스템
이를 이용하여
   - 어떤 파일이 변경되었는지 확인
   - 변경 내용을 기록
   - 이전 버전과 비교
   - 필요한 경우 이전 상태로 복구
   - GitHub 등의 원격 저장소와 협업
  
## 11. Git의 핵심 구조

- Working Directory(작업 디렉터리) : 실제 파일 수정
- Staging Area(스테이징 영역) : 커밋할 변경 선택
- Repository(저장소) : 커밋 이력 저장

`git add`는 변경 내용을 다음 커밋에 포함하도록 스테이징(다음 커밋에 기록할 변경 사항을 담아두는 작업)하는 작업
`git commit`은 스테이징된 내용을 저장소의 변경 이력으로 기록

## 실습 - Git 저장소 구축

### Step 1. 실습 디렉터리 생성

```bash
mkdir ~/cloud-lab/week01/git-lab
cd ~/cloud-lab/week01/git-lab

# Git 저장소 생성 및 초기 브랜치 main으로 명명
git init -b main    # branch main
```

과정 중에 `.git`이라는 숨김 디렉터리가 생성됨
`ls -la` 명령으로 확인 가능

### Step 2. Git 사용자 정보 설정

```bash
# 현재 디렉터리(Git 저장소)에서 커밋할 때 기록될 내 이름을 설정
git config --local user.name "Linux Learner"

# 현재 디렉터리에서 커밋할 때 내 이름 밑에 넘갈 연락처(이메일)를 설정 
git config --local user.email "learner@example.invalid"

# .git/config 파일에 저장된 모든 설정값을 키-값 형태로 출력
git config --local --list
```

`--local`은 현재 Git 저장소(현재 디렉터리)에만 설정을 적용한다는 의미
`--global`은 내 계정 전체의 저장소에 설정을 적용한다는 의미

### Step 3. 파일 생성

```bash
printf '# Cloud Linux Lab\n' > README.md
git status
```

### Step 4. 오류 재현 및 해결

`git add`를 실행하지 않아 스테이징 영역에 등록하지 않은 상태에서 커밋 시도

```bash
git commit -m "Initial Linux lab"
```

`git add`로 스테이징 영역에 등록 후 커밋

```bash
git add README.md
git commit -m "Initial Linux lab"

# 변경 이력 확인
git log --oneline
```

### Step 5. 변경 후 내용 비교

```bash
printf 'Week 1: Linux basic\n' >> README.md
git status

# 작업 디렉터리와 스테이징 되지 않은 변경 사항 비교
git diff

git add README.md
git diff --cached   # 스테이징된 변경 내용 비교
git commit -m "Update Linux study notes"
git log --oneline
```