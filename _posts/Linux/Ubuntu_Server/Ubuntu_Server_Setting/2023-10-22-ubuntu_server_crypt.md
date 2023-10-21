---
title:  "[Ubuntu Server] 암호화 (Crypt)"
excerpt: "Ubuntu Server 22.04 LTS에서 암호화를 진행한다."

categories:
  - Ubuntu Server
tags:
  - Ubuntu Server Setting

last_modified_at: 2023-10-11T00:16:00-05:00
---
## 1. 암호화 Volume 생성
- 루트 사용자 권한에서 진행한다. (`sudo su`)
- cryptsetup 패키지를 사용한다. Ubuntu Server에서는 기본 패키지로 제공한다.
- 암호화 하려는 Volume이 `/dev/sda`라고 가정한다.
- 아래 명령어로 적용 가능한 암호화 알고리즘들의 성능을 확인할 수 있다.

```bash
cryptsetup benchmark
```

- 원하는 암호화 알고리즘을 선택 후 다음과 같이 입력하여 암호화를 설정할 수 있다.

```bash
cryptsetup luksFormat --cipher aes-xts-plain64 --key-size 256 --hash sha256 --use-random /dev/sda
```

- 'YES' 입력 후 비밀번호를 입력한다.
- 아래의 명령어로 암호화한 Volume을 Open할 수 있다.

```bash
cryptsetup luksOpen /dev/sda cryptdata
```

- `ls -l /dev/mapper`를 입력하여 cryptdata Open 결과를 확인할 수 있다.
- 암호화한 Volume을 아래와 같이 Format할 수 있다.

```bash
mkfs.ext4 /dev/mapper/cryptdata
```

<br>

## 2. 저장소 자동 복호화 설정
- 부팅 시 저장소 복호화를 자동으로 시도하도록 설정할 수 있다. (부팅 단계에서 암호를 입력해야 한다.)
- 아래 명령어로 Volume의 UUID를 확인한다.

```bash
blikd /dev/sda
```

- 출력된 UUID를 복사하여 `/etc/crypttab` 파일에서 입력한다.
- nano, vim 등 원하는 편집기를 사용하여 작업한다. ex) `nano /etc/crypttab`

```bash
# <target name> <source device>         <key file>      <options>
cryptdata UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx none luks,discard
```

<br>

## 3. 저장소 자동 mount 설정
- 부팅 시 저장소 자동 mount까지 시도하려면 `/etc/fstab` 파일에서 작업한다.
- `nano /etc/fstab` 등으로 다음과 같이 입력한다.

```bash
/dev/mapper/cryptdata    /mnt/HDD1    ext4    defaults,nofail    0    1
```
