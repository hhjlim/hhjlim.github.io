---
title: "(Socket) Socket Programming - Unix/Linux"
categories:
  - Network
  - Socket Programming
tags:
  - Socket
---

Unix/Linux 환경에서의 Socket Programming 이야기 

## Introduction
- Ubuntu(라즈베리파이) 와 Windows 간에 TCP/IP, UDP socket Server-Client 프로그램을 만드는것이 목표이다.  
- 각각의 환경에서 사용하는 헤더파일, 함수명, 컴파일 명령 등이 다르기 때문에 이를 정리할것이고
- poll() 함수를 사용하여 다수의 소켓을 하나의 thread 에서 처리하게 할것이다.



## Compiler
- *`g++ (Ubuntu 14.2.0-4ubuntu2) 14.2.0`*  

## 필요한 헤더
- *`<sys/socket.h>`*  
- *`<arpa/inet.h>`*  
- *`<poll.h> `*  

---

### 1. Server - Socket
- 우선 소켓을 만들어주자
- TCP/IP 의 경우 *`SOCK_STREAM`*, UDP의 경우 *`SOCK_DGRAM`* 로 설정해주면 된다.  
- 소켓 또한 파일 디스크립터(FD) 이기 때문에, 정수로 반환된다.  

#### TCP Socket
- socket, bind, listen 까지는 모두 nonblocking 함수들이다.
- 이후 recv, accept 들의 경우 blocking 되기 때문에 poll() 함수에서 처리한다.

```cpp
// TCP/IP Socket Setup
int tcp_listen_sockfd = socket(AF_INET, SOCK_STREAM, 0);
if (tcp_listen_sockfd < 0) 
{
    perror("Failed to create TCP socket");
    return 1;
}

// Addr Setup
sockaddr_in server_addr;
memset(&server_addr, 0, sizeof(server_addr));
server_addr.sin_family = AF_INET;
server_addr.sin_port = htons(PORT);
server_addr.sin_addr.s_addr = INADDR_ANY;

//Bind
if (bind(tcp_listen_sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0) 
{
    perror("TCP socket binding failed");
    close(tcp_listen_sockfd);
    return 1;
}

//Listen
if (listen(tcp_listen_sockfd, SOMAXCONN) < 0)
{
    perror("TCP listen failed");
    close(tcp_listen_sockfd);
    return 1;
}

```

#### UDP Socket
- UDP의 경우 Socket 을 만들고 Bind 만 해주면 된다. 따로 연결 과정이 없다.
- 이후 Blocking 동작인 recvfrom(), sendto() 만 Poll() 에서 처리해주면 된다.

```cpp
  // UDP Socket Setup
  int udp_sockfd = socket(AF_INET, SOCK_DGRAM, 0);
  if (udp_sockfd < 0)
  {
      perror("Failed to Create UDP Socket");
      return 1;
  }
  
  //Addr Setup
  ...

  // UDP Socket bind
  if (bind(udp_sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0)
  {
      perror("Failed to bind UDP Socket");
      close(udp_sockfd);
      return 1;
  }
```

### 2. Server - poll()
- 우선 poll() 함수는 아래처럼 생겼다.

```cpp
int poll
(
  struct pollfd *fds,   // 감시할 파일 디스크립터들의 배열이다. 즉 socket 번호이다.
  nfds_t nfds,          // 몇개의 fds 가 있는지를 나타낸다.
  int timeout           // ms 단위로 대기, 음수면 무한대기.
);
```

- pollfd 구조체는 아래처럼 생겼다.

```cpp
struct pollfd 
{
    int   fd;        // 감시할 파일 디스크립터
    short events;    // 감시할 이벤트 (POLLIN,POLLPRI 등등)
    short revents;   // 실제로 발생한 이벤트 (poll 함수 호출 후 설정됨)
};
```

- 실제 사용 예시
- fds.[i].revents 는 커널에서 시그널을 보내주는 방식이다.

```cpp
    // pollfd array 3개 설정
    pollfd fds[3];

    // tcp listen socket
    fds[0].fd = tcp_listen_sockfd;
    fds[0].events = POLLIN;

    // udp socket
    fds[1].fd = udp_sockfd;
    fds[1].events = POLLIN;

    // client socket 은 accept 이후 설정 
    fds[2].fd = -1;
    fds[2].events = POLLIN;

    while (true) 
    {
        int poll_count = poll(fds, 3, -1); // 3개를 감시할 것이다.
        if (poll_count < 0) 
        {
            perror("poll failed aborting");
            break;
        }

        // 1.fds[0] TCP Listen 에 대한 accept 
        // poll() 함수 이후 revents 가 POLLIN 과 bitmatch 하다면 accept 하자.
        if (fds[0].revents & POLLIN)
        {
            sockaddr_in client_addr;
            socklen_t client_len = sizeof(client_addr);
            client_sockfd = accept(tcp_listen_sockfd, (struct sockaddr*)&client_addr, &client_len);
            if (client_sockfd < 0) 
            {
                perror("accept failed");
                continue;
            }

            // Add client_sockfd to fds[2]
            std::cout << "New TCP Client: " << inet_ntoa(client_addr.sin_addr) << std::endl;
            fds[2].fd = client_sockfd;
            fds[2].events = POLLIN;
        }

        // 2. fds[1] UDP Socket event 발생 recvfrom & sendto 
        if (fds[1].revents & POLLIN) 
        {
            char buffer[1024];
            sockaddr_in client_addr;
            socklen_t client_len = sizeof(client_addr);
            int bytes_received = recvfrom(udp_sockfd, buffer, sizeof(buffer) - 1, 0,
                                          (struct sockaddr*)&client_addr, &client_len);
            if (bytes_received > 0) 
            {
                buffer[bytes_received] = '\0';
                std::cout << "UDP Client [" << inet_ntoa(client_addr.sin_addr)
                          << ":" << ntohs(client_addr.sin_port) << "] msg: " << buffer << std::endl;
                // echo to client
                sendto(udp_sockfd, buffer, bytes_received, 0,
                       (struct sockaddr*)&client_addr, client_len);
            }
            else
            {
                perror("UDP recvfrom failed");
            }
        }

        // 3. TCP Client Socket Event 발생
        if (fds[2].revents & POLLIN) 
        {
            char buffer[1024];
            int bytes_received = recv(fds[2].fd, buffer, sizeof(buffer) - 1, 0);
            if (bytes_received > 0)
            {
                buffer[bytes_received] = '\0';
                std::cout << "Msg from TCP Client: " << buffer << std::endl;

                // send echo 
                send(fds[2].fd, buffer, bytes_received, 0);
            } 
            else if (bytes_received == 0) 
            {
                // close() from tcp client
                std::cout << "TCP client closed socket" << std::endl;
                close(fds[2].fd);
                fds[2].fd = -1;

            }
            else 
            {
                perror("error from TCP Client");
                close(fds[2].fd);
                fds[2].fd = -1;

            }
        }
    }
```