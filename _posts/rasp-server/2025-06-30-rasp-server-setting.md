---
title: "라즈베리파이 초기 세팅"
date: '2025-06-27'
categories: [Server,라즈베리파이]
tags: []
toc: true
---

## 운영체제/이미지 선택
사용용도에 따라서 선택지가 넓어진다. <br>
예를 들어 간단한 작업 + GPIO 같은 기본 기능을 활용해야 하면 `Raspbian` <br>
IoT 전용 OS `HAOS(Home Assitant OS)` 등등 여러가지 운영체제/이미지가 있다. <br>

필자의 목적은 아래와 같다.
- docker 통한 간단한 CI/CD 구축
- docker 통한 Homebridge <-> Homekit 연동
- MySQL 전용 데이터 베이스 구축
- Nginx 실습 등등..

{:.prompt-tip}
> Raspberry Pi에게 Ubuntu는 조금 무겁기 때문에 GUI 버전 대신 Ubuntu-server 버전을 추천한다.

주로 개발적인 목적이 크기 때문에, `Ubuntu os` 설치가 제일 편하다. <br>

## 라즈베리파이 업그레이드
현재 내 라즈베리파이의 사양은 `Raspberry Pi 4 8GB` 모델이다. <br>
기존 처럼 SD카드로 라즈베리파이를 사용하면 느려서 답답해 죽을것이다. <br>
![rasp](assets/post/rasp.png)
조금은 투자를 해서 저장 공간을 늘리자


## Ubuntu Server install
[Raspberry Pi Imager](https://www.raspberrypi.com/software/) | [Raspberry pi ubuntu iso](https://ubuntu.com/download/raspberry-pi) <br>

Imager 통해 설치도 가능하나, 필자는 iso 다운로드 후 balenaEtcher 사용을 지향한다.

## Package update,upgrade
```shell
sudo apt update && sudo apt upgrade -y
```
{: file="" }

```shell
sudo apt install net-tools 
sudo apt install openssh-server
```

## SSH setting
참 시간이 많이 걸린 작업이다. <br>
passwd 인증을 허용해도 계속 접속이 안되다가 해결방법을 찾았다.

```shell
# 서비스 확인
sudo systemctl status ssh
```

```shell
// 22 포트 허용
sudo ufw allow ssh
```

```shell
sudo vi /etc/ssh/sshd_config
```

```shell
# /etc/ssh/sshd_config
Include /etc/ssh/sshd_config.d/*.conf

Port 22
PermitRootLogin yes # 테스트 용, 계정 생성후 루트 접근 no
PasswordAuthentication yes # 비밀번호 인증 허용
PubkeyAuthentication no # 키 사용시 yes, 비밀번호 사용시 no
# KbdInteractiveAuthentication no 
```

{:.prompt-info}
> KbdInteractiveAuthentication는 ssh 클라이언트가 키보드 대화형 인증 방법을 사용하지 않도록 설정하는 옵션이다. <br>

이거 때문에 내 1시간이 날라갔다...

```shell
sudo systemctl restart ssh # ssh 재시작
```