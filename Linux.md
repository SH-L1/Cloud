# Linux 기초 및 WSL2 Ubuntu 환경 구축

> Windows에서 WSL2와 Ubuntu를 사용해 Linux의 기본 개념과 명령어를 익히는 실습 자료입니다.

## 목차

- [1. Linux란 무엇인가?](#1-linux란-무엇인가)
- [2. WSL2란 무엇인가?](#2-wsl2란-무엇인가)
- [3. 실습](#3-실습)

## 1. Linux란 무엇인가?

### 운영체제와 Linux 커널

운영체제(OS)는 CPU, 메모리, 저장 장치 같은 하드웨어 자원을 관리하고 프로그램이 실행될 수 있는 환경을 제공합니다. **커널(Kernel)**은 운영체제의 핵심 구성 요소로, 프로그램과 하드웨어 사이에서 자원 사용을 관리합니다.

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
| **Linux 커널** | CPU, 메모리, 프로세스, 장치 등 시스템 자원을 관리 |
| **Ubuntu** | Linux 커널과 셸, 패키지 및 시스템 관리 도구 등을 함께 제공하는 Linux 배포판 |
| **Bash** | 사용자가 입력한 명령을 해석하고 실행하는 셸 |

예를 들어, 현재 디렉터리의 파일 목록을 확인하려면 Bash에 `ls`를 입력합니다.

```bash
ls
```

명령 실행 흐름을 단순화하면 다음과 같습니다.

```text
사용자가 ls 입력 → Bash가 ls 실행 → 커널이 파일 시스템 접근 처리 → 파일 목록 출력
```

## 2. WSL2란 무엇인가?

WSL2(Windows Subsystem for Linux 2)는 Windows에서 Linux 환경을 사용할 수 있게 해 줍니다. WSL2는 경량 가상 머신에서 실제 Linux 커널을 실행하고, Ubuntu 같은 배포판의 사용자 공간을 제공합니다.

### PowerShell에서 Ubuntu 실행하기

```powershell
# WSL의 기본 구성 상태 확인
wsl --status

# 설치된 배포판, 실행 상태, WSL 버전 확인
wsl --list --verbose

# Ubuntu 실행
wsl -d Ubuntu
```

배포판 이름이 `Ubuntu-24.04`처럼 표시된다면 `wsl -d Ubuntu-24.04`처럼 해당 이름을 사용하세요. Ubuntu 터미널을 종료하고 PowerShell로 돌아가려면 `exit`을 입력합니다.

`wsl --list --verbose` 출력 예시:

```text
  NAME      STATE      VERSION
* Ubuntu    Stopped    2
```

- `Stopped`: 배포판이 설치되어 있지만 현재 실행 중이 아닙니다.
- `Running`: 배포판이 실행 중입니다.
- `VERSION`의 `2`: 해당 배포판이 WSL2를 사용합니다.

## 3. 실습

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

- `/home/user`는 `user` 계정의 홈 디렉터리 예시입니다. 실제 경로는 사용자 이름에 따라 달라집니다.
- `/mnt/c/Users/...`는 WSL에서 Windows의 C 드라이브에 접근할 때 사용하는 경로입니다.

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

`user` 부분은 실제 Linux 사용자 이름으로 표시됩니다.

### Step 4. 파일 생성과 조회

```bash
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

출력의 첫 번째 항목인 `-rw-r--r--`는 파일 종류와 권한을 나타냅니다. 사용자 이름과 날짜 등은 환경에 따라 달라질 수 있습니다.

### Step 5. 파일 권한 확인 및 복구

파일 권한을 `000`으로 바꾸면 소유자, 그룹, 그 외 사용자 모두 파일을 읽거나 쓸 수 없습니다.

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

실습 후에는 소유자에게 읽기·쓰기 권한을 주도록 복구합니다. `600`은 소유자에게만 읽기(`4`)와 쓰기(`2`) 권한을 부여합니다.

```bash
chmod 600 notes.txt
cat notes.txt
```
