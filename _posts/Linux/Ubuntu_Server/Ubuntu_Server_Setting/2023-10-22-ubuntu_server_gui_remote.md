---
title:  "[Ubuntu Server] GUI 원격 접속 (GUI Remote)"
excerpt: "Ubuntu Server 22.04 LTS에서 GUI 환경의 원격 접속을 설정한다."

categories:
  - [Linux] Ubuntu Server
tags:
  - [Linux] Ubuntu Server Setting

last_modified_at: 2023-10-22T02:40:00-05:00
---

## 1. 데스크톱 환경 설치

- 원격 접속한 client에게 GUI 환경을 제공하기 위해서는 Desktop 환경을 설치해야 한다.
- Server PC에 [GUI 환경을 구축](https://yeonhl.github.io/ubuntu%20server/ubuntu_server_gui/)하면서 설치했다면 해당 환경을 사용할 수 있다.
- Xfce, Gnome 등 원하는 환경을 1가지 설치하면 된다. Xfce가 상대적으로 더 가벼워서 권장된다고 한다.

```bash
# Xfce 설치
sudo apt install xubuntu-desktop
```

```bash
# Gnome 설치
sudo apt install ubuntu-desktop
```

<br>

## 2. 원격 접속 패키지 설치

- GUI 원격 접속 환경 제공을 위해선 패키지를 설치해야 한다. `xrdp` 패키지를 사용하여 진행한다.
- 충돌을 방지하기 위해서 기존 원격 접속 패키지를 삭제 후 진행할 수 있다.

```bash
# 기존 원격 접속 패키지 삭제
sudo apt purge realvnc-vnc-server tightvncserver xrdp
```

```bash
# 원격 접속 패키지 설치
sudo apt install xrdp
```
