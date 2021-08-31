### Extension II: C++ Network Programming

#### Introduction
**Protocol**: An agreement on how the programs that communicate across a computer network will communicate, and high-level decisions must be made about which program is expected to initiate communication and when responses are expected.  
  
For example, a Web server is typically thought of as a long-running program that sends network message only in response to requsts coming in from the network. The other side of the protocol is a Web client, such as a browser, which always initiates communication with the server. This organization into client and server is used by most network-aware applications.  
Deciding that the client always initiates request tends to simplify the protocol as well as the programs themselves.  
  
**Socket**: let programs attach to the local network at different ports.  
  
**Socket programming**: Socket programming is a way of connecting two nodes on a network to communicate with each other. One socket(nodes) listens on a particular port at an IP, while other socket reaches out to the other to form a connection.  
In the client and server organization, server forms the listener socket while client reaches out to the server.  

*State diagram for server and client model in Socket programming:*  
![Server_and_client](./SomePictures/Socket_server.png)
  
#### Socket Programming
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
  > 
Client-side states:
- _Create socket_: exactly the same with that of server's socket creation
- _Connect_: 
- _Send/Recv_: 

