# 2026-09-14 Linux 학습 내용

## 1. Linux 사용자 관리

### 사용자 목록 확인

Linux에서는 `/etc/passwd` 파일에 사용자 계정 정보가 저장되어 있다.

```bash
cat /etc/passwd
```

사용자 이름만 확인:

```bash
cut -d: -f1 /etc/passwd
```

일반 사용자 확인:

```bash
awk -F: '$3 >= 1000 {print $1}' /etc/passwd
```

현재 로그인한 사용자 확인:

```bash
whoami
```

현재 로그인한 사용자 목록:

```bash
who
```

---

## 2. 사용자 생성

사용자 생성:

```bash
sudo adduser student1
```

생성된 사용자의 정보를 확인:

```bash
id student1
```

`id` 명령어를 통해 UID, GID, 소속 그룹 등의 정보를 확인할 수 있다.

---

## 3. 사용자 삭제

사용자 삭제:

```bash
sudo userdel student1
```

사용자의 홈 디렉터리까지 삭제:

```bash
sudo userdel -r student1
```

또는:

```bash
sudo deluser --remove-home student1
```

### 사용자가 삭제되지 않는 경우

사용자가 현재 로그인되어 있거나 해당 사용자의 프로세스가 실행 중이면 삭제할 수 없다.

예:

```text
userdel: user student1 is currently used by process 4253
```

PID 확인:

```bash
ps -u student1
```

또는:

```bash
pgrep -a -u student1
```

특정 프로세스 확인:

```bash
ps -o pid,user,cmd -p 4253
```

사용자의 모든 프로세스를 종료:

```bash
sudo pkill -u student1
```

이후 다시 삭제:

```bash
sudo userdel -r student1
```

### 주의

프로세스를 강제로 종료하면 해당 사용자가 실행 중이던 작업이 종료될 수 있으므로 필요한 경우에만 사용한다.

---

## 4. Linux 파일 권한

Linux 파일과 디렉터리에는 권한이 존재한다.

기본 권한은 세 가지이다.

- `r` : 읽기(Read)
- `w` : 쓰기(Write)
- `x` : 실행(Execute)

권한은 다음 세 영역으로 나뉜다.

- `u` : 소유자(User)
- `g` : 그룹(Group)
- `o` : 기타 사용자(Others)

권한 확인:

```bash
ls -l
```

예:

```text
-rwxr-xr--
```

다음과 같이 해석한다.

```text
rwx | r-x | r--
 ↑      ↑     ↑
소유자  그룹  기타 사용자
```

---

## 5. chmod를 이용한 권한 변경

문자 방식:

```bash
chmod u+x file
chmod g+w file
chmod o-r file
```

숫자 방식에서는 다음 값을 사용한다.

```text
r = 4
w = 2
x = 1
```

따라서:

```text
rwx = 7
rw- = 6
r-x = 5
r-- = 4
```

예:

```bash
chmod 755 file
```

의미:

```text
소유자 : rwx = 7
그룹   : r-x = 5
기타   : r-x = 5
```

---

## 6. sudo와 관리자 권한

`sudo`는 일반 사용자가 관리자(root) 권한으로 명령을 실행할 수 있도록 한다.

예:

```bash
sudo userdel -r student1
```

모든 사용자가 `sudo`를 사용할 수 있는 것은 아니다.

sudo 권한이 없는 사용자가 관리자 명령을 실행하면 다음과 같은 메시지가 나타날 수 있다.

```text
user is not in the sudoers file
```

따라서 관리자 작업을 수행할 때는 현재 계정에 sudo 권한이 있는지 확인해야 한다.

---

## 7. Git 커밋과 Push

학습 내용을 Git에 추가:

```bash
git add .
```

커밋:

```bash
git commit -m "permission edit"
```

원격 저장소에 Push:

```bash
git push origin main
```

### Push가 거절되는 경우

다음과 같은 메시지가 발생할 수 있다.

```text
! [rejected] main -> main (fetch first)
```

이는 원격 저장소에 로컬 저장소에는 없는 커밋이 존재하기 때문이다.

단순히 다시 `git push`하는 것으로는 해결되지 않는다.

---

## 8. 로컬과 원격 브랜치가 서로 다른 경우

`git pull` 실행 시 다음과 같은 메시지가 나타날 수 있다.

```text
You have divergent branches and need to specify how to reconcile them.
```

이는 로컬과 원격에서 각각 새로운 커밋이 발생하여 브랜치가 서로 갈라진 상태이다.

이때 원격 변경사항을 먼저 반영하면서 로컬 커밋을 유지하려면:

```bash
git pull --rebase origin main
```

충돌이 없다면 이후:

```bash
git push origin main
```

충돌이 발생하면:

```bash
git status
```

로 충돌 파일을 확인하고 해결한다.

### 주의

다음 명령어를 무조건 사용하는 것은 위험하다.

```bash
git push --force
```

원격 저장소의 다른 작업 내용을 덮어쓸 수 있으므로, 먼저 `git pull --rebase` 등을 이용하여 변경사항을 안전하게 합치는 것이 좋다.

---

# 오늘 학습 핵심 키워드

- `/etc/passwd`
- `cat`
- `cut`
- `awk`
- `whoami`
- `who`
- `adduser`
- `userdel`
- `deluser`
- `id`
- `ps`
- `pgrep`
- `pkill`
- `sudo`
- `chmod`
- `r / w / x`
- `u / g / o`
- Linux 파일 권한
- Git commit
- Git push
- Git pull
- Git rebase
- Git branch divergence
- Push rejection

---

# 오늘 배운 핵심

오늘은 Linux에서 사용자를 생성하고 확인하고 삭제하는 방법과 사용자의 프로세스 및 권한을 확인하는 방법을 학습했다.

특히 사용자를 삭제할 때 해당 사용자의 프로세스가 실행 중이면 삭제할 수 없다는 것을 확인했고, `ps`, `pgrep`, `pkill` 등을 이용하여 프로세스를 확인하고 종료하는 방법을 학습했다.

또한 Linux의 `rwx` 파일 권한과 `chmod`, `sudo`의 기본적인 사용 방법을 학습했다.

Git에서는 로컬과 원격 저장소의 커밋이 서로 달라 Push가 거절되는 상황을 경험했다. 이때 `git pull --rebase`를 이용하여 원격 변경사항을 먼저 반영한 뒤 Push하는 방법을 학습했다.
