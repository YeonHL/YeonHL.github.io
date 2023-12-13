---
title: "[Ubuntu Server] GUI 환경 구성 (GUI)"
excerpt: "Ubuntu Server 22.04 LTS를 GUI 환경으로 사용할 수 있도록 설정한다."

categories:
  - Linux Ubuntu Server
tags:
  - Linux Ubuntu Server Setting

last_modified_at: 2023-10-22T02:45:00-05:00
---

> Ubuntu Server PC를 직접 사용하는 경우가 많고 GUI 환경이 편리할 경우 이를 구현할 수 있다.

## 1. 데스크톱 환경 설치

- [GUI Remote 환경 구축](https://yeonhl.github.io/ubuntu%20server/ubuntu_server_gui_remote/)에 사용한 데스크톱 환경을 그대로 사용할 수 있다.
- 직접 설치하기를 원할 경우 아래와 같이 설치할 수 있다.

```bash
# Gnome 표준 설치
sudo apt install ubuntu-desktop -y

# Gnome 최소 설치
sudo apt install --no-install-recommends ubuntu-desktop -y
```

<br>

## 2. Desktop Manager 설치

- 설치한 데스크톱 환경을 Server PC에서 실행할 Desktop Manager 패키지가 필요하다.
- gdm(Gnome Desktop Manager)는 무거워서 상대적으로 가벼운 lightdm이 선호된다고 한다.

### 1) gdm 사용 (Default)

```bash
# (확인용) 기본 DM 찾기
cat /etc/Xll/default-display-manager
/usr/sbin/gdm3

# 시작 커맨드
sudo service gdm3 start
```

```bash
# 종료 커맨드
sudo service gdm3 stop
```

### 2) lightdm 사용

- 설치 중 default dm 사용을 물어보면 lightdm으로 변경해주면 된다.

```bash
# 설치
sudo apt install lightdm

# (확인용) 기본 DM 찾기
cat /etc/X11/default-display-manager
/usr/sbin/lightdm

# 시작 커맨드
sudo service lightdm start
```

### 3) SLiM 사용

```bash
sudo apt install slim
```

<br>

## 3. 실행

- `startx`로 Ubuntu Desktop을 실행할 수 있다.
- 최소 설치로 진행한 경우 아래의 추가 패키지 설치 후 사용을 권장한다.

```bash
# 상단 메뉴 시간 추가
sudo apt install indicator-datetime

# hud service not connected 오류 해결
sudo apt install indicator-appmenu-tools

# 볼륨 조절 아이콘 추가
sudo apt install indicator-applet-complete

# 상단 메뉴 시간 추가
sudo apt-get install indicator-datetime
```
