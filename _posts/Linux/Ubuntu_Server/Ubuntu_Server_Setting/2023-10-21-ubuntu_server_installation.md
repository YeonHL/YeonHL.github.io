---
title:  "[Ubuntu Server] 설치 (Installation)"
excerpt: "폐쇄망 네트워크에서 Ubuntu Server 22.04 LTS 설치를 진행한다."

categories:
  - Ubuntu Server
tags:
  - Ubuntu Server Setting

last_modified_at: 2023-10-21T23:45:00-05:00
---
## 1. 설치 준비
- [Ubuntu 22.04.3 LTS](https://releases.ubuntu.com/jammy/) 버전의 Ubuntu Server 기준으로 진행한다.
- 가상 VM이 아닌 실제 PC에 세팅하는 것을 기준으로 작성했다. 가상 VM에 설치하기를 원할 경우 이미지를 받아서 VM에 삽입 후 [2. Network 설정]부터 진행하면 된다.
- 별도의 폐쇄망을 구축할 때를 기준으로 진행했다. 실제 인터넷 환경에 설치할 경우 달라지는 항목이 있을 것이다.


### 1) 설치 디스크 만들기
- 이미지 다운 후 [Rufus](https://rufus.ie/en/)를 사용하여 디스크를 제작했다.
- 아래와 같이 장치, 이미지를 선택 후 다른 설정 없이 시작으로 진행했다. 본인의 환경에 맞게 내용을 수정해도 무방하다.

<br>

<img src="https://i.ibb.co/pZj9ZjQ/installation-1.png" alt="installation-1" width="400" class="img_post">

<br>

- 디스크 제작 후에는 BIOS에서 부팅 순서를 설정하거나(첫 부팅 화면에서 F2나 DEL키로 들어가는 경우가 많다.) Boot Menu에 들어가서 USB 등 제작한 디스크를 부팅하면 된다.

<br>

## 2. Network 설정
- 부팅 후 언어 설정, 키보드 설정은 본인 환경에 맞게 진행해준다.
- 이후 PC의 Network를 설정해야 한다. 무선 랜카드가 있는 경우 wlan으로 지정하면 `Choose a visible network`에서 Wifi 연결이 가능하다.
- IP 주소는 다음 항목들을 입력한다.

### 1) **Subnet**
 - Network 주소 + Host 주소, Host 주소는 0으로 표시한다.
 - /24와 같이 Subnet이 몇자리인지 나타내야 한다. 24는 예시의 `192.168.10`까지가 해당된다. Subnet 규칙을 참고하여 숫자 지정하기
> ex) **Subnet**: 192.168.10.0/24

### 2) **Address**
- 서버에 할당할 주소를 입력하면 된다.
> ex) **Address**: 192.168.10.47

### 3) **Gateway**
- 네트워크의 Gateway를 입력하면 된다.
- 네트워크 구성에 따라 달라지므로 본인의 환경에 맞게 입력하면 된다.
> ex) **Gateway**: 192.168.10.1

### 4) **Name Servers**
- Domain 이름을 IP 주소로 변환하거나 IP 주소를 도메인 이름으로 변환한다.
- Ubuntu 서버는 네임 서버 정보를 설정하여 도메인 이름 해석을 수행하며 주로 DNS(Domain Name System) 서버를 지칭한다.
> ex) **Name Servers**: 8.8.8.8

### 5) **Search Domains**
- 호스트 이름을 입력할 때 기본적으로 검색되는 도메인 이름 목록이다.
- 서버에서 Domain Name을 지정하지 않으면 Search Domain의 Domain을 참조하여 Host name을 해석한다.
- 일반 사용자는 필요하지 않다면 빈칸으로 둬도 무방하다.

<br>

## 3. Proxy Server 설정
- 원하는 Proxy 설정이 있다면 입력하면 된다. 따로 없다면 빈칸으로 두기

<br>

## 4. Mirror Server 설정
- 원하는 주소가 있다면 입력하면 된다. 기본으로 제공하는 `http://kr.archive.ubuntu.com/ubuntu`를 사용해도 무방하다.
- 인터넷 연결이 있어야 업데이트가 수행된다.
- Kakao Mirror 사이트인 `http://mirror.kakao.com`으로 변경하면 속도가 더 빠르다고 한다. 원한다면 설정하기

<br>

## 5. Disk 설정
- 기본 설정으로 전체 디스크가 지정된다. 원하는 설정이 있다면 `Custom storage layout`을 선택해야 한다.
- LVM 등 원하는 설정 선택하기, `Encrypt the LVM group with LUKS`로 암호화를 수행할 수 있다.

<br>

## 6. Profile 설정
- 비밀번호는 원하는 값으로 지정하면 된다.

### 1) **Your name**
- 실제 본인 이름이나 사용할 username을 동일하게 입력하면 된다.

### 2) **Your server's name**
- 서버의 이름을 설정하면 된다.

### 3) **Pick a username**
- username을 입력한다. Ubuntu 환경에서 사용할 username이다.

<br>

## 7. SSH 설정
- 서버 용도가 목적이므로 원격 접속을 위해 OpenSSH 설치를 권장한다.

<br>

## 8. Server Snap
- 추가 설치할 프로그램을 선택한다. 필요한 프로그램이 없다면 건너뛰면 된다.
