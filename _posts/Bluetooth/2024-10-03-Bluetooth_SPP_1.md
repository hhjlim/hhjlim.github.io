---
title: "(Bluetooth) SPP(Serial Port Profile) protocol - 1"
categories:
  - Bluetooth
tags:
  - Bluetooth
  - bluez
  - Raspberry Pi
---

Bluetooth SPP Protocol 을 사용하여 통신하는 이야기 - 1편

## Introduction
SPP(Serial Port Profile) 을 이용한 블루투스 통신에 관한 예제를 찾던 도중에, 예전에, 공모전에 사용하였던 블루투스 통신에 관한 내용을 복기할 겸 글을 하나 작성해 두면 좋겠다고 생각하였다. 일종의 Cheat sheet처럼..그때 당시에는 내가 SPP를 사용하고 있는지도 잘 몰랐던 거 같은데 어떻게 했나 싶기도 하다.  
아무튼 이번에는 MAN 페이지도좀 읽어보고 커널 소스도 한번 찾아보면서 내가 뭘 했었는지 다시 한번 리뷰하고자 한다. 

<div class="mermaid"> 
  graph LR;
    RaspberryPi[Raspberry Pi]  <-- SPP (Serial Port Profile) -->  AndroidPhone[Android Phone];
</div>


## Prerequisites
먼저 Linux 환경에서 Bluez 를 사용할것이기 때문에, 라즈베리파이를 사용하기로 하였다.  
(Bluez 는 리눅스 시스템에서 사용하는 블루투스 프로토콜 스택)  
라즈베리파이에서 SPP Server 를 열고,  
미리 페어링 해둔 안드로이드 앱에서 클라이언트로 접속해 SPP 프로토콜로 통신하는것이 목적이다.  

### 필요한 패키지
- *`libbluetooth-dev`*  

### SDP 활성화 하기
사실 이부분에서 막혔었기 때문에 기록해둔다.  
SPP 프로토콜을 사용하려면, 블루투스 데몬이 *`--compat`* 옵션으로 실행중이여야  
SDP(Service Discovery Protocol) 서버를 활성화 할 수 있다..!  
보안상의 이유로 기본적으로 요즘 나오는 장비들은 꺼져있다고 한다.   
(외부에서 서버의 서비스 정보를 조회할 수 있기 때문.. 더 자세한사항은 다음 편에서 다루어볼 예정)  

아래 예시 처럼 서비스 파일을 수정하도록 하자.

```bash
sudo nano /lib/systemd/system/bluetooth.service
```

수정 전:
```bash
ExecStart=/usr/lib/bluetooth/bluetoothd
```

수정 후:
```bash
ExecStart=/usr/lib/bluetooth/bluetoothd --compat
```

저장하고, 블루투스 데몬을 재시작하자.
```bash
sudo systemctl daemon-reload
sudo systemctl restart bluetooth
```

SDP 서버 동작 확인
```bash
sudo sdptool browse local
```

이제 SPP 서비스를 등록하자
```bash
sudo sdptool add SP
```

다시한번 상태 확인
```bash
sudo sdptool browse local
```

아래 처럼 나오면 성공
```yaml
Service Name: Serial Port
Service RecHandle: 0x10000
Service Class ID List:
  "Serial Port" (0x1101)
Protocol Descriptor List:
  "L2CAP" (0x0100)
  "RFCOMM" (0x0003)
    Channel: 1
Language Base Attr List:
  code_ISO639: 0x454e
  encoding:    0x6a
  base_offset: 0x100
Profile Descriptor List:
  "Serial Port" (0x1101)
    Version: 0x0100
```

이런 개념적인 내용을 몰라서... 아무리 Listen 중인 녀석을 내가 연결해보려고해도... 안되지... 반성하게 된다...  
연결 되는 시리즈에 이런 내용들을 정리할 예정이다.  

## SPP Server Setup - (Raspberry pi)

이제 라즈베리파이에서는 아래 코드를 컴파일해서 실행해주면 SPP 서버 준비는 끝이다.

```cpp
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <unistd.h>
#include <sys/socket.h>
#include <bluetooth/bluetooth.h>
#include <bluetooth/rfcomm.h>

int main()
{
    int server_sock, client_sock;
    struct sockaddr_rc loc_addr = { 0 }, rem_addr = { 0 };
    char buf[1024] = { 0 };
    socklen_t opt = sizeof(rem_addr);

    // 1. Create Server Socket
    server_sock = socket(AF_BLUETOOTH, SOCK_STREAM, BTPROTO_RFCOMM);
    if (server_sock < 0) {
        perror("Failed to create socket");
        exit(EXIT_FAILURE);
    }
    else{
        printf("server_sock = [%d]\n",server_sock);
    }

    // 2. binding with local bluetooth adaptor
    loc_addr.rc_family = AF_BLUETOOTH;
    memset(&loc_addr.rc_bdaddr, 0, sizeof(loc_addr.rc_bdaddr)); // initialize with 0
    loc_addr.rc_channel = (uint8_t)1; // Set channel to 1

    if (bind(server_sock, (struct sockaddr *)&loc_addr, sizeof(loc_addr)) < 0) {
        perror("Bind failed");
        close(server_sock);
        exit(EXIT_FAILURE);
    }
    else{
        printf("bind success server_sock = [%d]\n",server_sock);
    }

    // 3. Listen
    if (listen(server_sock, 1) < 0) {
        perror("listen Failed");
        close(server_sock);
        exit(EXIT_FAILURE);
    }
    else{
        printf("listen server_sock = [%d]\n",server_sock);
    }

    printf("Waiting for client...\n");

    // 4. Accept
    client_sock = accept(server_sock, (struct sockaddr *)&rem_addr, &opt);
    if (client_sock < 0) {
        perror("accept failed");
        close(server_sock);
        exit(EXIT_FAILURE);
    }

    // 5. print client info
    char client_addr_str[18] = { 0 };
    ba2str(&rem_addr.rc_bdaddr, client_addr_str);
    printf("client_addr_str: %s\n", client_addr_str);

    // 6. read data
    int bytes_read = read(client_sock, buf, sizeof(buf));
    if (bytes_read > 0) {
        printf("buf: %s\n", buf);
    }

    // 7. Always Close socket after use
    close(client_sock);
    close(server_sock);

    return 0;
}

```

아래 명령어로 컴파일
```bash
g++ -o spp_server spp_server.cpp -lbluetooth
sudo ./spp_server 
```

## Client - (Android Phone)
- Play store 에서 *`Serial Bluetooth Termial`* 앱 다운로드
- 페어링한 상태에서 Listen 중인 Raspberry pi 에 연결 시도
- 연결 후 메시지 전송

## Result - (Raspberry pi terminal)
```bash
negi@negi-pi5:~/Projects/bluez $ sudo ./spp_server 
server_sock = [3]
bind success server_sock = [3]
listen server_sock = [3]
Waiting for client... ## 이후 앱에서 메시지 전송
client_addr_str: XX:XX:XX:XX:XX:XX ## 검열
buf: hello bluetooth
```