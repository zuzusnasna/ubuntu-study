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

## 6. 사용자 그룹 관리

Linux에서는 사용자를 하나 이상의 그룹에 소속시켜 파일 접근 권한 등을 관리할 수 있다.

### 현재 사용자의 그룹 확인

```bash
groups
```

특정 사용자의 그룹 확인:

```bash
groups student1
```

또는 UID, GID, 그룹 정보를 함께 확인할 수 있다.

```bash
id student1
```

### 그룹 생성

```bash
sudo groupadd animals
```

### 사용자를 보조 그룹에 추가

```bash
sudo usermod -aG animals dog
```

`-aG`의 의미:

- `-a` : 기존 보조 그룹을 유지하면서 추가
- `-G` : 보조 그룹 지정

기존 그룹을 유지해야 하므로 일반적으로 `-aG` 형태로 사용하는 것이 안전하다.

그룹 변경 후에는 로그아웃 후 다시 로그인해야 변경된 그룹이 세션에 적용될 수 있다.

확인:

```bash
groups dog
```

### 사용자의 기본 그룹 변경

```bash
sudo usermod -g animals dog
```

`-g`는 사용자의 기본(primary) 그룹을 변경한다.

### 그룹에서 사용자 제거

```bash
sudo gpasswd -d dog animals
```

---

## 7. chown을 이용한 소유자 및 그룹 변경

`chown`은 파일이나 디렉터리의 소유자(owner)와 그룹(group)을 변경할 때 사용한다.

기본 형식:

```bash
chown 사용자:그룹 파일
```

예:

```bash
sudo chown dog:animals msg_from_dog
```

디렉터리와 내부 파일까지 재귀적으로 변경하려면 `-R` 옵션을 사용한다.

```bash
sudo chown -R dog:animals msg_from_dog
```

### 일반 사용자가 chown을 실행할 때

일반 사용자가 다른 소유자로 파일의 소유권을 변경하려 하면 다음과 같은 오류가 발생할 수 있다.

```text
Operation not permitted
```

또한 현재 사용자에게 sudo 권한이 없으면:

```text
user is not in the sudoers file
```

와 같은 메시지가 나타난다.

이 경우 관리자(root) 또는 sudo 권한이 있는 계정으로 작업해야 한다.

예를 들어 `dog`가 sudo 권한이 없다면 `jusang`처럼 sudo 권한이 있는 계정으로 로그인한 뒤:

```bash
sudo chown -R dog:animals /tmp/playground/msg_from_dog
```

를 실행할 수 있다.

### `chown`과 그룹 가입의 차이

다음 두 작업은 서로 다르다.

```bash
sudo usermod -aG animals dog
```

→ `dog` 사용자를 `animals` 그룹의 구성원으로 추가

```bash
sudo chown dog:animals msg_from_dog
```

→ `msg_from_dog` 파일의 소유자를 `dog`, 소유 그룹을 `animals`로 변경

---

## 8. sudo와 관리자 권한

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

# Ctrl + Alt + t

새로운 터미널 열기

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
- `chown`
- `groupadd`
- `usermod`
- `gpasswd`
- `r / w / x`
- `u / g / o`
- Linux 파일 권한
- Linux 사용자 그룹
- 파일 소유자와 소유 그룹
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

사용자와 그룹 관리에서는 `groupadd`, `usermod`, `gpasswd`를 이용해 그룹을 생성하고 사용자를 그룹에 추가하거나 제거하는 방법을 학습했다. 또한 `chown`을 이용해 파일의 소유자와 소유 그룹을 변경하는 방법과 sudo 권한이 없는 사용자가 소유권 변경을 수행할 수 없는 상황도 직접 확인했다.

Git에서는 로컬과 원격 저장소의 커밋이 서로 달라 Push가 거절되는 상황을 경험했다. 이때 `git pull --rebase`를 이용하여 원격 변경사항을 먼저 반영한 뒤 Push하는 방법을 학습했다.
