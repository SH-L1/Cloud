# Linux 기초 및 WSL2 Ubuntu 환경 구축

> **학습 목표**
> - Linux 커널과 배포판의 역할 이해
> - PowerShell에서 WSL2 Ubuntu 실행
> - Linux 기본 명령어와 파일 권한 실습

## 목차

- [1. Linux란 무엇인가?](#1-linux란-무엇인가)
- [2. WSL2란 무엇인가?](#2-wsl2란-무엇인가)
- [실습 - 파일과 디렉터리 생성](#실습---파일과-디렉터리-생성)
- [3. Linux 파일 시스템 심화](#3-linux-파일-시스템-심화)
- [실습 - 파일 관리 및 장애 진단](#실습---파일-관리-및-장애-진단)

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
| 소유자 권한 | rw- | 읽기, 쓰기 가능 |
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

## 사용자·그룹·권한 관리

### Linux의 사용자 권한 구분

Linux는 왜 사용자마다 권한을 구분하는가

- ex) 웹 서버 실행 환경
  - 관리자 : 서버 설정을 변경, 프로그램을 설치
  - 개발자 : 애플리케이션 코드 수정
  - 웹 서버 프로세스 : 필요한 웹 파일을 읽고, 지정 위치에만 데이터 기록

모든 사용자와 프로그램에 관리자 권한이 존재한다면,  
웹 서버 프로그램 하나에 문제가 생겼을 때 시스템 전체에 영향을 줄 가능성 커짐

===> Linux는 파일마다 소유자, 그룹, 기타 사용자에 대한 접근 권한을 분리함

### Linux 파일 권한 구조

파일 종류/소유자/그룹/기타 사용자  
각 권한은 읽기(`r`), 쓰기(`w`), 실행(`x`)의 조합으로 표현

| 권한 | 숫자 |
| --- | --- |
|읽기(r)|4|
|쓰기(w)|2|
|실행(x)|1|
|권한 없음(-)|0|

- ex) `chmod 750` 이라면,
  - 소유자 : 7 = 4 + 2 + 1 -> `rwx`
  - 그룹 : 5 = 4 + 1 -> `r-x`
  - 기타 사용자 : 0 -> `---`
- 따라서 권한은 `rwxr-x---`

주의점 : 파일의 `rwx`와 디렉터리의 `rwx`는 의미가 조금 다름  
특히, 디렉터리의 `x`는 해당 경로 안으로 이동하거나 알고 있는 파일을 이름을 통해 항목에 접근할 수 있는 권한을 말함

## 실습 - 사용자와 그룹 확인

```bash
whoami    # 현재 사용자 이름
id        # UID, 기본 GID, 소속 그룹 조회
groups    # 사용자가 속한 그룹 조회
id -u     # 현재 사용자의 UID만 출력
id -gn    # 현재 사용자의 기본 그룹 이름 출력

```

| 값 | 설명 |
| --- | --- |
| uid=1000(user) | 사용자 고유 ID |
| gid=1000(user) | 사용자의 대표(기본) 그룹 |
| groups=1000(user) | 사용자가 속해 있는 모든 그룹의 전체 목록 (주 그룹 + 보조 그룹들) |
| 4(adm) | `/var/log` 아래에 있는 시스템 로그 파일들을 읽을 수 있는 권한을 가진 그룹 |
| 24(cdrom) | CD/DVD-ROM 드라이브 장치 접근하고 사용 가능한 권한을 가진 그룹 |
| 27(sudo) | `sudo` 명령어를 쓸 수 있는(관리자 특권) 그룹 |
| 30(dip) | 네트워크 통신 연결을 직접 맺고 끊을 수 있는 권한을 가진 그룹 |
| 46(plugdev) | 외부 장치(USB, 외장 하드 등)를 컴퓨터에 꽂았을 때 마운트(접근)할 수 있는 권한을 가진 그룹 |
| 100(users) | 일반 사용자 그룹 |

## Linux 사용자와 그룹

```text
사용자: user
예시 UID: 1000

소속 그룹: user, sudo, 기타 보조 그룹(adm, cdrom, ...)
```

### UID와 GID가 필요한 이유

Linux 커널은 파일에 접근하려는 프로세스의 사용자·그룹 식별 정보와 파일의 소유권·접근 권한을 비교함

1. 프로세스가 파일 소유자라면 소유자 권한을 확인
2. 그렇지 않고 파일의 소유 그룹에 속한다면 그룹 권한 확인
3. 둘 다 해당하지 않으면 기타 사용자 권한 확인

파일 권한 검사에서 이 세 범위를 합산하는 것(`rw-r--r--`를 전부 합산하는 것)이 아니라,  
해당하는 권한 범위(`rw-`, `r--`, `r--`)를 선택함

## 파일과 디렉터리의 권한 차이

Linusx의 rwx의 의미는 파일과 디렉터리에서 차이가 존재

| 권한 | 일반 파일 | 디렉터리 |
| --- | --- | --- |
| `r`(Read) | 파일 내용 읽기 | 내부 항목 이름 조회 |
| `w`(Write) | 파일 내용 수정 | 내부 항목 생성·삭제·이름 변경 |
| `x`(Execute) | 실행 가능한 파일 실행 | 디렉터리 진입·경로 탐색 |

- `w`(Write)의 경우, 디렉터리 안의 항목을 생성·삭제·이름 변경하기 위해서는 `w`와 `x` 권한 모두 필요함

## 실습 - 파일 권한 변경과 오류 복구

```bash
mkdir -p ~/cloud-lab/week01/permissions
cd ~/cloud-lab/week01/permissions

printf 'Linux permissions practice\n' > example.txt

ls -l sample.txt    # 해당 권한은 시스템 umask에 따라 달라질 수 있음
```

**umask란?**
 - 기본 최대 권한(777 or 666)을 잡아두고 거기에 umask 설정값을 적용하여 안전한 기본 권한을 만듦
 - Ubuntu/Debian 등 대부분의 리눅스는 umask 기본값이 022
 - [기본 최대 값] - [umask 값] = [디렉터리 혹은 일반 파일 권한]
 - ex) 777 - 022 = 755 (rwxr-xr-x), 666 - 022 = 644 (rw-r--r--)
 - `umask` 명령어로 마스크 값 확인 가능

```bash
chmod 400 sample.txt
printf 'New line\n' >> sample.txt   # >>는 기존 파일의 끝에 내용을 추가하는 연산자

# 권한 오류 발생
# Permission denied

chmod 640 sample.txt
printf 'New line\n' >> sample.txt
cat sample.txt
```

## 실습 - 디렉터리 실행 권한의 역할

