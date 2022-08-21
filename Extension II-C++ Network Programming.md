### Extension II: C++ Network Programming

#### Introduction
<span id ="introduction"></span>
**Protocol**: An agreement on how the programs that communicate across a computer network will communicate, and high-level decisions must be made about which program is expected to initiate communication and when responses are expected.  
  
For example, a Web server is typically thought of as a long-running program that sends network message only in response to requsts coming in from the network. The other side of the protocol is a Web client, such as a browser, which always initiates communication with the server. This organization into client and server is used by most network-aware applications.  
Deciding that the client always initiates request tends to simplify the protocol as well as the programs themselves.  
  
**Socket**: let programs attach to the local network at different ports.  
  
**Socket programming**: Socket programming is a way of connecting two nodes on a network to communicate with each other. One socket(nodes) listens on a particular port at an IP, while other socket reaches out to the other to form a connection.  
In the client and server organization, server forms the listener socket while client reaches out to the server.  

*State diagram for server and client model in Socket programming:*  
![Server_and_client](./SomePictures/Socket_server.png)
  
#### Socket Programming
<span id = "socket"></span>
Server-side states:  
- _Create socket_: `int sockfd = socket(int domain, int type, int protocol);`  
  > `sockfd`: socket descriptor, like a file-handle, is an integer  
  > `domain`: communication domain, AF_INET(IPv4 Protocol) or AF_INET6(IPv6 Protocol)  
  > `type`: communication type, SOCK_STREAM(TCP, connection oriented, reliable) or SOCK_DGRAM(UDP, connectionless)  
  > `protocol`: Protocol value for Internet Protocol(IP), which is the same number that appears on protocol field in the IP header of packet.  
- _Set socket option_: `int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);`
  > This helps in manipulating options for the socket referred by the file descriptor sockfd.  
  > **It is completely optional**, but it helps in reuse of address and port. Prevents error such as: “address already in use”.  
- _Bind_: `int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`
  > After creation of socket, `bind` function is going to be used to bind the socket to the address and port number specified by the `addr`(this data structure is defined in _sys/socket.h_) argument.  
- _Listen for connection request_: `int listen(int sockfd, int backlog);`
  > A nodes that once called this function will be set in passive mode, where it waits for the client to approach the server to make a connection.  
  > The `backlog` specifies the maximum length to which the queue of pending connections for sockfd may grow. If a connection request arrive when the queue is full, the client may receive an error with an indication of ECONNREFUSED.
- _Accept a connection request_: `int new_socket = accept(int sockfd, struct sockaddr * addr);` 
  > It extracts the first connection request on the queue of pending connections for the listening socket specified by `sockfd`, and creates a new connected socket and then returns a new file descriptor referring to that socket.  
  > At this point, connection is established between client and server, and they are ready to transfer data.
- _Send/Recv_: 
  > `ssize_t recv(int socket, void *buffer, size_t length, int flags);`  
  > `ssize_t recvfrom(int socket, void *buffer, size_t length, int flags, struct sockaddr *address, socklen_t *address_len);`  
  > `ssize_t recvmsg(int socket, struct msghdr *message, int flags);`  
  > `ssize_t send(int socket, const void *message, size_t length, int flags);`  
  > `ssize_t sendmsg(int socket, const struct msghdr *message, int flags);`  
  > `ssize_t sendto(int socket, const void *message, size_t length, int flags, const struct sockaddr *dest_addr, socklen_t dest_len);`  

Client-side states:

- _Create socket_: exactly the same with that of server's socket creation
- _Connect_: `int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`
  > It connects the socket referred to by the file descriptor `sockfd` to the address specified by `addr`. Sever's address and port is specified in `addr`.
- _Send/Recv_: 
  > `ssize_t recv(int socket, void *buffer, size_t length, int flags);`  
  > `ssize_t recvfrom(int socket, void *buffer, size_t length, int flags, struct sockaddr *address, socklen_t *address_len);`  
  > `ssize_t recvmsg(int socket, struct msghdr *message, int flags);`  
  > `ssize_t send(int socket, const void *message, size_t length, int flags);`  
  > `ssize_t sendmsg(int socket, const struct msghdr *message, int flags);`  
  > `ssize_t sendto(int socket, const void *message, size_t length, int flags, const struct sockaddr *dest_addr, socklen_t dest_len);`  
  
Visit [sys_socket.h(0p) - Linux manual page](https://man7.org/linux/man-pages/man0/sys_socket.h.0p.html) for more details.  
  
#### Implementation  
<span id = "implementation"></span>
Here is an example copy from [Socket Programming in C/C++ - GeeksforGeeks](https://www.geeksforgeeks.org/socket-programming-cc/) of exchaning one hello message between server and client to demonstrate the client and server organization.  
Server-side code:  
```c
// Server side C/C++ program to demonstrate Socket programming
#include <unistd.h>
#include <stdio.h>
#include <sys/socket.h>
#include <stdlib.h>
#include <netinet/in.h>
#include <string.h>
#define PORT 8080
int main(int argc, char const *argv[])
{
    int server_fd, new_socket, valread;
    struct sockaddr_in address;
    int opt = 1;
    int addrlen = sizeof(address);
    char buffer[1024] = {0};
    char *hello = "Hello from server";
       
    // Creating socket file descriptor
    if ((server_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0)
    {
        perror("socket failed");
        exit(EXIT_FAILURE);
    }
       
    // Forcefully attaching socket to the port 8080
    if (setsockopt(server_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT,
                                                  &opt, sizeof(opt)))
    {
        perror("setsockopt");
        exit(EXIT_FAILURE);
    }
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons( PORT );
       
    // Forcefully attaching socket to the port 8080
    if (bind(server_fd, (struct sockaddr *)&address, 
                                 sizeof(address))<0)
    {
        perror("bind failed");
        exit(EXIT_FAILURE);
    }
    if (listen(server_fd, 3) < 0)
    {
        perror("listen");
        exit(EXIT_FAILURE);
    }
    if ((new_socket = accept(server_fd, (struct sockaddr *)&address, 
                       (socklen_t*)&addrlen))<0)
    {
        perror("accept");
        exit(EXIT_FAILURE);
    }
    valread = read( new_socket , buffer, 1024);
    printf("%s\n",buffer );
    send(new_socket , hello , strlen(hello) , 0 );
    printf("Hello message sent\n");
    return 0;
}
```
  
Client-side code:  
```c++
// Client side C/C++ program to demonstrate Socket programming
#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <string.h>
#define PORT 8080

int main(int argc, char const *argv[])
{
	int sock = 0, valread;
	struct sockaddr_in serv_addr;
	char *hello = "Hello from client";
	char buffer[1024] = {0};
	if ((sock = socket(AF_INET, SOCK_STREAM, 0)) < 0)
	{
		printf("\n Socket creation error \n");
		return -1;
	}

	serv_addr.sin_family = AF_INET;
	serv_addr.sin_port = htons(PORT);
	
	// Convert IPv4 and IPv6 addresses from text to binary form
	if(inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr)<=0)
	{
		printf("\nInvalid address/ Address not supported \n");
		return -1;
	}

	if (connect(sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0)
	{
		printf("\nConnection Failed \n");
		return -1;
	}
	send(sock , hello , strlen(hello) , 0 );
	printf("Hello message sent\n");
	valread = read( sock , buffer, 1024);
	printf("%s\n",buffer );
	return 0;
}
```
  
Compiling:
```
gcc client.c -o client
gcc server.c -o server
```
  
Output:
```
Client:Hello message sent
Hello from server
Server:Hello from client
Hello message sent
```
  
ref.  
[Socket Programming in C/C++ - GeeksforGeeks](https://www.geeksforgeeks.org/socket-programming-cc/)  
