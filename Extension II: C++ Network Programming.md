### Extension II: C++ Network Programming

#### Introduction
Protocol: An agreement on how the programs that communicate across a computer network will communicate.  
Before delving into the design details of a protocol, high-level decisions must be made abput which program is expected to initiate communication and when responses are expected.  
For example, a Web server is typically thought of as a long-running program that sends network message only in response to requsts coming in from the network. The other side of the protocol is a Web client, such as a browser, which always initiates communication with the server. This organization into client and server is used by most network-aware applications.  
Deciding that the client always initiates request tends to simplify the protocol as well as the programs themselves.  


Socket: let programs attach to the local network at different ports.  

