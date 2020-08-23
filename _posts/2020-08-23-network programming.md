#网络编程

##socket API
### socket

API原型：  
&emsp;&emsp;int socket(int af, int type, int protocol);  
作用：  
&emsp;&emsp;创建一个套接字  
返回值：  
&emsp;&emsp;正确：创建的套接字描述符  
&emsp;&emsp;错误：INVALID_SOCKET，可以通过WSAGetLastError() 获取相应错误代码  
参数：  
&emsp;&emsp;int af：一个地址描述  
&emsp;&emsp;&emsp;&emsp;有效值：AF_INET  
&emsp;&emsp;int type：指定socket类型  
&emsp;&emsp;&emsp;&emsp;有效值:  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;SOCK_STREAM。提供基于连接的、可靠的、有序的和双向的字节流，使用TCP传输协议  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;SOCK_DGRAM。提供无连接的、不可靠的和使用固定大小缓冲区的数据报服务，使用UDP传输协议  
&emsp;&emsp;int protocol：套接字所用的协议  
&emsp;&emsp;&emsp;&emsp;有效值：  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;IPPROTO_TCP，对应TCP  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;IPPROTO_UDP，对应UDP  
头文件：  
&emsp;&emsp;sys/socket.h  
###bind
API 原型：  
&emsp;&emsp;int bind(int sockfd , const struct sockaddr* my_addr, socklen_t addrlen);  
作用：  
&emsp;&emsp;将本地地址与套接字描述符绑定  
返回值：  
&emsp;&emsp;正确：0  
&emsp;&emsp;错误：-1，可以通过WSAGetLastError() 获取相应错误代码  
参数：  
&emsp;&emsp;int sockfd：未绑定地址的套接字描述符  
&emsp;&emsp;const struct sockaddr * my_addr：待绑定的套接字地址，通常不直接操作sockaddr，而使用等价数据结构sockaddr_in  
&emsp;&emsp;socklen_t addrlen：结构体sockaddr的长度  
头文件：  
&emsp;&emsp;sys/socket.h  

相关结构体定义：  
&emsp;&emsp;struct sockaddr {  
    		&emsp;&emsp;&emsp;&emsp;u_short sa_family;	// 2字节，IPV4下使用AF_INET，代表TCP/IP协议族  
    		&emsp;&emsp;&emsp;&emsp;char sa_data[14];	// 协议地址  
&emsp;&emsp;};  
&emsp;&emsp;头文件：  
&emsp;&emsp;&emsp;&emsp;sys/types.h  

&emsp;&emsp;struct sockaddr_in {  
&emsp;&emsp;&emsp;&emsp;short sin_family;			// 2字节，IPV4下使用AF_INET，代表TCP/IP协议族  
&emsp;&emsp;&emsp;&emsp;unsigned short sin_port;	// 端口号，通常使用htons(8000)的形式进行赋值  
&emsp;&emsp;&emsp;&emsp;struct in_addr sin_addr;		// IP地址，通常使用inet_addr(“192.168.1.1”) 的形式进行赋值  
&emsp;&emsp;&emsp;&emsp;unsigned char sin_zero[8];	// 没有实际意义，用于同结构体sockaddr对齐  
&emsp;&emsp;}  
&emsp;&emsp;头文件：  
&emsp;&emsp;&emsp;&emsp;netinet/in.h  

&emsp;&emsp;typedef uint32_t in_addr_t;  
&emsp;&emsp;struct in_addr {  
&emsp;&emsp;&emsp;&emsp;in_addr_t s_addr;	// IP地址，通常使用inet_addr(“192.168.1.1”) 的形式进行赋值  
&emsp;&emsp;}  
&emsp;&emsp;头文件：  
&emsp;&emsp;&emsp;&emsp;arpa/inet.h  




