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

### `chown`과 그룹 가입의 차이

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

## 9. mv를 이용한 파일과 디렉터리 이동 및 이름 변경

`mv`는 파일이나 디렉터리를 다른 위치로 이동하거나 이름을 변경할 때 사용하는 명령어이다.

기본 형식:

```bash
mv [옵션] 원본 대상
```

### 파일을 다른 디렉터리로 이동

```bash
mv file.txt test/
```

`file.txt`를 `test` 디렉터리로 이동한다.

### 파일 이름 변경

```bash
mv old.txt new.txt
```

같은 디렉터리에서 대상 이름을 다르게 지정하면 파일 이름이 변경된다.

### 이동하면서 이름 변경

```bash
mv old.txt test/new.txt
```

파일을 `test` 디렉터리로 이동하면서 `new.txt`라는 이름으로 변경할 수 있다.

### 디렉터리 이름 변경

```bash
mv old_directory new_directory
```

디렉터리 자체의 이름을 변경할 수 있으며 내부에 있는 파일도 그대로 유지된다.

### 여러 파일을 한 번에 이동

```bash
mv file1.txt file2.txt test/
```

여러 파일을 지정하면 마지막 인자인 `test/`가 대상 디렉터리가 된다.

### 자주 사용하는 옵션

```bash
mv -i file.txt test/
```

대상에 같은 이름의 파일이 있을 경우 덮어쓰기 전에 확인한다.

```bash
mv -n file.txt test/
```

기존 파일을 덮어쓰지 않는다.

```bash
mv -v file.txt test/
```

실제로 어떤 이동 작업이 수행되는지 출력한다.

### `mv`와 `cp`의 차이

```bash
mv file.txt test/
```

→ 원본 파일이 이동하므로 기존 위치에서는 사라진다.

```bash
cp file.txt test/
```

→ 원본을 유지하면서 복사본을 만든다.

따라서 `mv`는 파일을 이동하거나 이름을 변경할 때 사용하고, `cp`는 원본을 유지하면서 복사할 때 사용한다.

---

## 10. ping을 이용한 네트워크 연결 확인

`ping`은 특정 호스트에 ICMP Echo Request를 보내 네트워크 연결 상태와 응답 여부를 확인할 때 사용하는 명령어이다.

기본 사용법:

```bash
ping 8.8.8.8
```

도메인 이름으로도 확인할 수 있다.

```bash
ping google.com
```

응답이 정상적으로 오면 네트워크 연결과 대상 호스트의 응답 여부를 확인할 수 있다.

### 자주 사용하는 옵션

```bash
ping -c 4 google.com
```

4번만 요청을 보내고 종료한다.

```bash
ping -c 1 8.8.8.8
```

한 번만 연결을 확인한다.

### ping 결과에서 확인할 내용

```text
64 bytes from ...: icmp_seq=1 ttl=... time=...
```

- `icmp_seq` : 요청 순서
- `ttl` : 패킷의 TTL 값
- `time` : 요청을 보내고 응답을 받기까지 걸린 시간

`ping`은 단순히 인터넷이 되는지 확인하는 것뿐만 아니라 특정 서버까지 네트워크 통신이 가능한지 빠르게 확인할 때 활용할 수 있다.

---

## 11. ps를 이용한 프로세스 확인

`ps`는 현재 실행 중인 프로세스의 정보를 확인하는 명령어이다.

기본 사용법:

```bash
ps
```

현재 터미널에서 실행 중인 프로세스를 확인할 수 있다.

모든 사용자의 프로세스를 확인:

```bash
ps aux
```

프로세스의 PID와 실행 명령 등을 확인할 수 있다.

특정 사용자의 프로세스 확인:

```bash
ps -u student1
```

프로세스 ID를 포함하여 특정 프로세스를 자세히 확인:

```bash
ps -o pid,user,cmd -p 4253
```

### PID

`PID(Process ID)`는 실행 중인 각각의 프로세스를 구분하기 위한 고유한 번호이다.

`kill` 명령어를 사용할 때 특정 프로세스를 지정하는 기준으로 사용한다.

---

## 12. kill을 이용한 프로세스 종료

`kill`은 PID를 이용하여 실행 중인 프로세스에 종료 등의 신호(signal)를 보내는 명령어이다.

기본 사용법:

```bash
kill PID
```

예:

```bash
kill 4253
```

특정 프로세스에 종료 신호를 보낸다.

### 강제 종료

```bash
kill -9 4253
```

`SIGKILL` 신호를 보내 프로세스를 강제로 종료한다.

일반적으로는 먼저 기본 `kill`을 사용하고, 정상적으로 종료되지 않을 때 `kill -9`를 사용하는 것이 좋다.

### ps와 kill을 함께 사용

프로세스를 종료할 때는 먼저 `ps` 등으로 PID를 확인한 뒤 `kill`을 사용할 수 있다.

```bash
ps aux
kill 4253
```

특정 사용자의 프로세스를 확인한 뒤 종료할 수도 있다.

```bash
ps -u student1
sudo kill 4253
```

사용자의 모든 프로세스를 종료하려면 `pkill`을 사용할 수도 있다.

```bash
sudo pkill -u student1
```

### `kill`과 `pkill`의 차이

```bash
kill 4253
```

→ 특정 PID의 프로세스에 신호를 보낸다.

```bash
pkill -u student1
```

→ 특정 사용자에 속한 프로세스 등을 조건으로 여러 프로세스에 신호를 보낼 수 있다.

프로세스를 강제로 종료하면 실행 중인 작업이 손실될 수 있으므로 필요한 경우에만 사용한다.

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
- `kill`
- `ping`
- `sudo`
- `chmod`
- `chown`
- `groupadd`
- `usermod`
- `gpasswd`
- `mv`
- `r / w / x`
- `u / g / o`
- PID
- 프로세스
- 네트워크 연결 확인
- Linux 파일 권한
- Linux 사용자 그룹
- 파일 소유자와 소유 그룹
- 파일 및 디렉터리 이동
- 파일 및 디렉터리 이름 변경
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

추가로 `mv` 명령어를 학습하면서 파일과 디렉터리를 다른 위치로 이동하거나 이름을 변경하는 방법을 익혔다. `mv`는 단순히 파일을 이동하는 것뿐만 아니라 같은 디렉터리에서 다른 이름을 지정하여 이름을 변경할 수도 있으며, 이동과 이름 변경을 한 번에 처리할 수도 있다는 것을 확인했다. 또한 `-i`, `-n`, `-v` 옵션을 통해 덮어쓰기 방지와 작업 확인 등의 기능을 사용할 수 있다는 것도 학습했다.

네트워크 관련해서는 `ping`을 사용하여 특정 IP나 도메인으로 ICMP 요청을 보내 네트워크 연결과 응답 상태를 확인하는 방법을 학습했다. 또한 `-c` 옵션을 이용해 지정한 횟수만큼만 요청을 보내는 방법을 익혔다.

프로세스 관리에서는 `ps`를 이용해 실행 중인 프로세스를 조회하고 PID를 확인하는 방법을 학습했다. 확인한 PID를 `kill` 명령어에 사용하여 특정 프로세스에 종료 신호를 보내는 방법과 `kill -9`를 이용한 강제 종료 방법을 익혔다. 또한 `kill`과 `pkill`의 차이를 통해 특정 PID를 대상으로 종료하는 방법과 조건에 따라 여러 프로세스를 종료하는 방법의 차이를 이해했다.

Git에서는 로컬과 원격 저장소의 커밋이 서로 달라 Push가 거절되는 상황을 경험했다. 이때 `git pull --rebase`를 이용하여 원격 변경사항을 먼저 반영한 뒤 Push하는 방법을 학습했다.
