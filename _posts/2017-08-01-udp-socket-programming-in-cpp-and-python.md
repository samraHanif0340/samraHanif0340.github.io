---
title: "UDP socket programming in C++ and Python"
tags: 
  - tcp
  - udp
  - socket
  - cpp
  - python
categories:
  - programming     
---

Sample C++ code for transmitting data via UDP socket

```c++
int main()
{
  std::string IP = "127.0.0.1";
  int PORT = 8888;

  try
  {
    WSASession Session;
    UDPSocket Socket;
    std::string data = "hello world";
    char buffer[100];

    while (1)
    {
      std::cout << "Enter data to transmit : " << std::endl;
      std::getline(std::cin, data);
      Socket.SendTo(IP, PORT, data.c_str(), data.size());
    }
  }
  catch (std::exception &ex)
  {
    std::cout << ex.what();
  }
}
```

Sample C++ code for receiving data via UDP socket

```c++
int main()
{
  int PORT = 8888;

  try
  {
    WSASession Session;
    UDPSocket Socket;
    char buffer[100];

    Socket.Bind(PORT);
    while (1)
    {
      sockaddr_in add = Socket.RecvFrom(buffer, sizeof(buffer));
      std::string input(buffer);
      std::cout << "Data received: " << input << std::endl;
    }
  }
  catch (std::system_error& e)
  {
    std::cout << e.what();
  }
}
```

Sample Python code for receiving data via UDP socket

```python

import socket
BUFFER_LEN = 100 #in bytes

def initUDP(IP, port):
    #Create a datagram socket
    sock = socket.socket(socket.AF_INET, # Internet
                         socket.SOCK_DGRAM) # UDP
    #Enable immediate reuse of IP address
    sock.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)
    #Bind the socket to the port
    sock.bind((IP, port))
    #Set a timeout so the socket does not block indefinitely when trying to receive data
    sock.settimeout(0.5)

    return sock

def readUDP(sock):
    try:
        data, addr = sock.recvfrom(BUFFER_LEN) 
    except socket.timeout as e: 
        return b'Error'
    except Exception as e:
        return b'Error'
    else: 
        return data 
```