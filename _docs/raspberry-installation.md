---
title: "Raspberry pie installation"
permalink: /docs/raspberry-installation/
excerpt: "Raspberry pie 초기 설정 방법"
last_modified_at: 2019-12-20T16:40:00+09:00
redirect_from:
  - /theme-setup/
toc: true
toc_label: "목차"
toc_icon: "list"
---

Raspberry pie 개봉후 처음 설정하는 방법

# 준비물

- Hardware
  - Raspberry zero, 3, 4, ...
  - 본체
  - 충전기 usb B, C
  - SD Card 
  - SD Card reader
  - Wifi Router
- Software
  - Raspbian
  - etcher
  - Wifi Router admin password

# download

## Raspbian Download

3가지중 선택

- Raspbian Buster with desktop and recommended software
- Raspbian Buster with desktop
- Raspbian Buster Lite

url: [https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/)

## SD card formatter

~~맥에서 FAT-32 포맷을 하기위해서 필요~~

url: [https://www.sdcard.org/downloads/formatter/eula_mac/index.html](https://www.sdcard.org/downloads/formatter/eula_mac/index.html)

## Etcher

url: [https://www.balena.io/etcher/](https://www.balena.io/etcher/)

# SD card에 이미지 복사

## SD card format

~~SD card formatter로 포맷~~

> 굳이 포멧 안해도 되는 듯?

## 이미지 복사

1. Etcher 실행
2. 20XX-XX-XX-raspbian-XX-XX.img 선택
3. 타겟 SD card 선택
4. Flash

![alt flash](/images/rasp/2019-12-20_15.27.24.png)

# 설정파일 추가하기

## SD Card로 이동

```bash
$ cd /Volumes/boot
```

## ssh 활성화

### ssh 파일 추가

```bash
$ vi ssh
:wq
```

## wifi 접속 설정

> 5G 대역은 안되는 것으로 확인됨..

### 파일 생성

```bash
$ vi wpa_supplicant.conf
```

### Wifi 비밀번호가 있을 경우

```bash
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=KO

network={
    ssid="yourSSID"
    psk="yourPassword"
}
```
### Wifi 비밀번호가 없을 경우

```bash
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=KO

network={
    ssid="yourSSID"
    key_mgmt=NONE
}
```

# 구동

SD Card unmount후 Raspberry pie 에 SD Card 삽입 후 부팅

# Wifi IP 검색

라우터 로그인 후 IP 찾기

![alt wifi](/images/rasp/2019-12-20_16.04.51.png)

# 접속

```bash
$ ssh pi@192.168.x.x
```

초기 패스워드: raspberry

# 설정

```bash
$ sudo raspi-config
```

## 패스워드 변경

- Change User Password

## Update

- Update: 최신버전으로 업데이트

## 지역 설정

- Localisation Options > I1 Change Locale > ko_KR.UTF-8 UTF-8 선택

- Localisation Options > I2 Change Timezone > GMT+9 선택

## VNC 설정

- Interfacing Options > VNC > YES 

- Advenced Options > A5 Resolution > DMT Mode 85 1280x720 60Hz 16:9 선택

> Resolution을 지정하지 않을 경우 화면 표시가 안될 수 있음.

Resolution 변경후 Finish를 선택하면 재시작됨

# VNC viewer로 확인

## viewer download

hdmi 출력을 한번도 안했을 경우 전용 뷰어로만 접속이 가능

url: [https://www.realvnc.com/en/connect/download/viewer/](https://www.realvnc.com/en/connect/download/viewer/)

![alt vnc](/images/rasp/2019-12-20_18.22.22.png)

> 위의 Welcome 화면에서 자연스럽게 Next 진행하면 위험.. Wifi설정 페이지가 나오면서 현재 연결된 Wifi connection이 끊어지면서 다시 접속할 수 없게됨.. 

## 한글 설정

한글 폰트가 없을 경우, 위와 같이 한글이 깨짐(휴지통).

```bash
$ sudo apt-get install fonts-unfonts-core -y
$ sudo reboot
```

폰트를 설치하고 재시작하면 됨.

# Application

## node install

```bash
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```

bluetooth module인 noble을 사용하기 위해 8버전을 사용해야함..

### 이미 다른 버전이 깔려 있는 경우

```bash
$ sudo npm i -g n
$ sudo n 8
$ exit
```

n module 설치 후 8버전으로 설치함.

설치후 ssh 세션을 나갔다가 들어와야 버전 반영됨 

### rasp zero일 경우

직접다운로드 받고 옮겨야함..

```bash
$ curl -o nodejs.tar.gz https://nodejs.org/dist/v8.9.4/node-v8.9.4-linux-armv6l.tar.gz
$ tar -xzf nodejs.tar.gz
$ sudo cp -r node-v8.9.4-linux-armv6l/* /usr/local/
$ reboot
```

## yarn install

```bash
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
$ sudo apt-get update && sudo apt-get install yarn
```


## Project

### new

```bash
$ mkdir prj && cd "_$"
$ yarn init
```

### eslint

```bash
# in project
$ yarn add -D eslint
$ yarn eslint --init
```

## pm2

```bash
$ sudo npm i pm2 -g
$ sudo pm2 install pm2-logrotate

# log retain 7day
$ sudo pm2 set pm2-logrotate:retain 7

# after run
$ sudo pm2 save
$ sudo pm2 startup
```

# System setup

## Reboot

일정시간마다 재시작

```bash
sudo crontab -e
$ 0 1 * * * /sbin/shutdown -r now
$ reboot
```

- 자정 1시에 재시작
- 설정 후 한번 재시작해야 적용됨

## Watchdog

### additional module install

```bash
$ sudo modprobe bcm2835_wdt
$ echo "bcm2835_wdt" >> /etc/modules 
```

> 해야할 지 안해야할 지 잘 모름 다음 세팅때 없이 한번 해봐야함.

### install

```bash
$ sudo apt-get install watchdog chkconfig
$ chkconfig watchdog on
```

### edit

```bash
$ sudo nano /etc/watchdog.conf
# below under text input

file      = /var/log/syslog
change      = 1800

watchdog-device  = /dev/watchdog

realtime    = yes
priority    = 1
```

### start

```bash
$ sudo service watchdog start
```


