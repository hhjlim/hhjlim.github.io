---
title: "(Socket) Socket Programming - Windows"
categories:
  - Network
  - Socket Programming
tags:
  - Socket
---

Windows 환경에서의 Socket Programming 이야기 

## Introduction
- Ubuntu(라즈베리파이) 와 Windows 간에 TCP/IP, UDP socket Server-Client 프로그램을 만드는것이 목표이다.  
- 각각의 환경에서 사용하는 헤더파일, 함수명, 컴파일 명령 등이 다르기 때문에 이를 정리할것이고
- poll() 함수를 사용하여 다수의 소켓을 하나의 thread 에서 처리하게 할것이다.



## Compiler
- *`g++.exe (x86_64-win32-seh-rev0, Built by MinGW-W64 project) 8.1.0`*  

## 필요한 헤더
- *`<winsock2.h>`*  
- *`<ws2tcpip.h>`*  

---

### 1. Client - Socket
- Winsock2 는 Windows 환경에서 사용되는 소켓 라이브러리 헤더, 컴파일 옵션으로 아래와 같이 포함시킬 수 있다.
> *`g++ -o client.exe client.cpp -lws2_32`*  
> visual studio 의 경우 *`#pragma comment(lib, "ws2_32.lib")`*
- 크게 다른것은 없고, *`WSAStartup()`* 을 통해서 ws2_32.lib 를 초기화 시켜주는 과정이 필요하다.  

```cpp
void initialize_winsock() 
{
    WSADATA wsaData;
    // 0x0202를 의미하며 WinSock DLL 2.2 버전을 Load 함
    int init_result = WSAStartup(MAKEWORD(2,2), &wsaData);
    if (init_result != 0) 
    {
        std::cerr << "WSAStartup Failed: " << init_result << std::endl;
        exit(1);
    }
}
```

#### TCP Socket - connect

```cpp
void  tcp_client_setup() 
{
    // make socket for tcp
    std::cout << "TCP Client Setup Start" << std::endl;

    tcp_sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (tcp_sockfd == INVALID_SOCKET)
    {
        std::cerr << "TCP socket failed: " << WSAGetLastError() << std::endl;
        exit(1);
    }

    // set server addr

    memset(&server_addr, 0, sizeof(server_addr));          // init sockaddr_in with 0
    server_addr.sin_family = AF_INET;                      // IPv4 = AF_INET
    server_addr.sin_port = htons(PORT);                    // port

    // server ip settings
    server_addr.sin_addr.s_addr = inet_addr(SERVER_IP);
    if (server_addr.sin_addr.s_addr == INADDR_NONE) 
    {
        std::cerr << "IP convert failed" << std::endl;
        closesocket(tcp_sockfd);
        exit(1);
    }

    // connect
    if (connect(tcp_sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0) 
    {
        std::cerr << "TCP server connect failed: " << WSAGetLastError() << std::endl;
        closesocket(tcp_sockfd);
        exit(1);
    }

    tcp_connected = true;
    std::cout << "TCP Connected" << std::endl;

}

```

#### UDP Socket

```cpp
void udp_client_setup() 
{
    std::cout << "UDP Client Setup Start" << std::endl;
    udp_sockfd = socket(AF_INET, SOCK_DGRAM, 0);
    if (udp_sockfd == INVALID_SOCKET)
    {
        std::cerr << "UDP Socket failed " << WSAGetLastError() << std::endl;
        exit(1);
    }

    udp_connected = true;
    std::cout << "UDP Connected" << std::endl;
}
```

### 2. Client - msg전송
- TCP/IP 의 경우 *`send()`* *`recv()`*  
- UDP 의 경우 *`sendto()`* *`recvfrom()`* 으로 메시지를 처리해준다. 
- 통신을 종료하고 싶으면  *`closesocket()`* 
- 이후 프로그램을 종료하기 전에 *`WSACleanup()`* 으로 정리해준다.

```cpp
void send_msg()
{
    std::string input;
    std::cout << "Input msg: (tcp msg / udp msg / tcp exit / udp exit): ";
    while(true)
    {
        std::getline(std::cin,input);
        if(strcmp(input.c_str(),"EXIT") == 0)
        {
            std::cout<<"End of Program exiting now.."<<std::endl;
            closesocket(tcp_sockfd);
            closesocket(udp_sockfd);
            break;
        }
        else
        {
            send(tcp_sockfd, input.c_str(), input.length(), 0);
            char buffer[1024];
            int bytes_received = recv(tcp_sockfd, buffer, sizeof(buffer) - 1, 0);
            if (bytes_received > 0) 
            {
                buffer[bytes_received] = '\0';
                std::cout << "TCP Server echo: " << buffer << std::endl;
            }

            sendto(udp_sockfd, input.c_str(), input.length(), 0, (struct sockaddr*)&server_addr, sizeof(server_addr));
            char buffer2[1024];
            sockaddr_in from_addr;
            int from_len = sizeof(from_addr);
            int bytes_received2 = recvfrom(udp_sockfd, buffer2, sizeof(buffer2) - 1, 0, (struct sockaddr*)&from_addr, &from_len);
            if (bytes_received2 > 0) 
            {
                buffer2[bytes_received2] = '\0';
                std::cout << "UDP Server echo: " << buffer2 << std::endl;
            }
        }
    }

}

int main() {
    // init window socket with WSAStartup
    initialize_winsock();
    tcp_client_setup();
    udp_client_setup();

    send_msg();

    //exit
    WSACleanup();

    return 0;
}
```