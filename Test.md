# 파일 시스템 구축

```bash
mkdir -p ~/cloud-lab/week01/final-lab
cd ~/cloud-lab/week01/final-lab

mkdir docs backup logs
printf 'Server version 1' > docs/server.txt
touch logs/app.log

pwd
```

# 파일 복사 및 검색

```bash
cp docs/server.txt backup/server.txt
mv docs/server.txt docs/server-v2.txt

find . -type f -name "*.txt"
test -f ./docs/server-v2.txt &&
test -f ./backup/server.txt &&
echo "Both file exist"
```

# Linux 권한 오류 재현과 복구

## 파일 쓰기 권한 오류

```bash
cd ~/cloud-lab/week01/final-lab

chmod 400 docs/server-v2.txt
printf '\nTest!\n' >> docs/server-v2.txt

chmod 640 docs/server-v2.txt
printf '\nTest!\n' >> docs/server-v2.txt

ls -l docs/server-v2.txt
cat docs/server-v2.txt
```

## 디렉터리 접근 권한 오류

```bash
chmod 600 logs
cd ~/cloud-lab/week01/final-lab/logs

chmod 700 logs
cd ~/cloud-lab/week01/final-lab/logs

pwd
```

# Git으로 변경 이력 관리

```bash
cd ~/cloud-lab/week01/final-lab
git init -b main

git add .
git commit -m "Initial server environment"

printf '\nServer version 2\n' >> docs/server-v2.txt
git diff

git add docs/server-v2.txt
git diff --cached
git commit -m "Update server version"

find . -name "server-v2.txt"
git log --oneline
git status
```