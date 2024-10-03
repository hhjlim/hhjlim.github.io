---
title: "(Bluetooth) Bluetooth communication using SPP(Serial Prot Profile) protocol - 1"
categories:
  - Bluetooth
tags:
  - Bluetooth
  - bluez
  - Raspberry Pi
---

Bluetooth SPP Protocol 을 사용하여 통신하는 이야기 - 1편

## What am I trying to do?
SPP(Serial Prot Profile) 을 이용한 블루투스 통신에 관한 예제를 찾던 도중에, 예전에, 공모전에 사용하였던 블루투스 통신에 관한 내용을 복기할 겸 글을 하나 작성해 두면 좋겠다고 생각하였다. 일종의 Cheat sheet처럼..그때 당시에는 내가 SPP를 사용하고 있는지도 잘 몰랐던 거 같은데 어떻게 했나 싶기도 하다.  
아무튼 이번에는 MAN 페이지도좀 읽어보고 커널 소스도 한번 찾아보면서 내가 뭘 했었는지 다시 한번 리뷰하고자 한다. 

```mermaid
graph LR
    RaspberryPi[Raspberry Pi] -- SPP (Serial Port Profile) --> AndroidPhone[Android Phone]
```

## Setting - Raspberry Pi
먼저 Linux 환경에서 Bluez 를 사용할것이기 때문에, 라즈베리파이를 사용하기로 하였다.  
(Bluez 는 리눅스 시스템에서 사용하는 블루투스 프로토콜 스택)
