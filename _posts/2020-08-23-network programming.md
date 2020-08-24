# 网络编程

## socket API
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
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;SOCK_STREAM。提供基于连接的、可靠的、有序的和双向的字节流，TCP  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;SOCK_DGRAM。提供无连接的、不可靠的和使用固定大小缓冲区的数据报服务，UDP  
&emsp;&emsp;int protocol：套接字所用的协议  
&emsp;&emsp;&emsp;&emsp;有效值：  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;IPPROTO_TCP，对应TCP  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;IPPROTO_UDP，对应UDP  
头文件：  
&emsp;&emsp;sys/socket.h  

***
### bind  

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

***

## 文件描述符的非阻塞模式

### connect
涉及fd：  
&emsp;&emsp;客户端套接字文件描述符client_socket_fd。  
阻塞模式：  
&emsp;&emsp;客户端调用connect()会触发TCP的三次握手过程，仅在连接建立成功或出错时才返回。  
非阻塞模式：  
&emsp;&emsp;客户端调用connect()，如果返回0，则连接建立成功；如果返回-1，则检查errno，如果值为EINPROGRESS，则连接正在建立。为了控制连接的建立时间，将该socket   fd加入到epoll的监控事件集合中，如果超时时间内连接成功建立，则该socket fd变为可写，否则该socket fd变为既可读又可写，此时调用getsockopt()，当errno等于0时表示只可写。  
***
### listen  
涉及fd：  
&emsp;&emsp;服务端套接字文件描述符(监听套接字文件描述符)listen_socket_fd。  
阻塞模式或非阻塞模式对于listen()调用是没有区别的，因为listen调用仅仅做两件事：  
&emsp;&emsp;当调用socket()创建一个套接字时，默认创建一个主动套接字，即它是一个将调用connect()发起连接的客户端套接字。listen()调用把一个未连接的套接字转换为一个被动套接字，指示内核应该接受指向该套接字的连接请求。根据TCP状态转换图，调用listen()将导致套接字从CLOSED状态转换为LISTEN状态；  
&emsp;&emsp;使用第二个参数设置套接字队列的最大长度。  
***
### accept  
涉及fd：  
&emsp;&emsp;服务端套接字文件描述符(监听套接字文件描述符)listen_socket_fd。  
阻塞模式：  
&emsp;&emsp;客户端发起连接，完成三次握手之后，在服务端还没有调用accept()之前，客户端就调用close()关闭连接，此时客户端发送的是RST报文而不是FIN报文，所以该连接在内核中就被断开了，但是此时用户层并不知道，如果此时服务端经过epoll_wait()后，调用accept()，由于连接已经断开，因此accept()调用会一直阻塞。  
非阻塞模式：  
&emsp;&emsp;如果有连接存在，则accept()正常返回；如果没有连接存在，或者连接被断开了，则accept()返回不同的错误码，此时属于正常情况，不用处理。  
***
### epoll_wait  
涉及fd：  
&emsp;&emsp;客户端套接字文件描述符client_socket_fd；  
&emsp;&emsp;服务端套接字文件描述符(监听套接字文件描述符)listen_socket_fd；  
&emsp;&emsp;连接文件描述符connection_fd。  
阻塞模式：  
&emsp;&emsp;只支持EPOLLLT模式。  
非阻塞模式：  
&emsp;&emsp;支持EPOLLET和EPOLLLT模式。  
***
### read  
涉及fd：  
&emsp;&emsp;连接文件描述符connection_fd。  
阻塞模式：  
&emsp;&emsp;当接收缓冲区中没有数据时，read()调用阻塞，直到有数据到来才返回；当缓冲区中的数据量小于期望读取的数据量时，返回实际读取的字节数；当缓冲区中的数据量不小于期望读取的数据量时，读取期望读取的字节数，返回实际读取的字节数。  
非阻塞模式：  
&emsp;&emsp;当接收缓冲区中没有数据时，read()调用立即返回-1，errno为EWOULDBLOCK或EAGAIN，如果errno为别的值，则表明读取失败；当缓冲区中的数据量小于期望读取的数据量时，返回实际读取的字节数；当缓冲区中的数据量不小于期望读取的数据量时，读取期望读取的字节数，返回实际读取的字节数。  

| | 阻塞模式 | 非阻塞模式 |  
|:-|:---------|:-----------|
| 没有数据 | 阻塞 | 返回-1，errno为EWOULDBLOCK或EAGAIN |
| 数据不足（小于期望值） | 返回实际读取的字节数  | 返回实际读取的字节数 |
| 数据足够（不小于期望值） | 读取期望读取的字节数，返回实际读取的字节数  | 读取期望读取的字节数，返回实际读取的字节数 |  

***
### write  
涉及fd：  
&emsp;&emsp;连接文件描述符connection_fd。  
阻塞模式：  
&emsp;&emsp;如果发送缓冲区没有空间或者空间不足以拷贝所有待发送数据的话，write()调用阻塞；如果空间足够，则拷贝所有数据到发送缓冲区，然后返回实际拷贝的字节数。  
非阻塞模式：  
&emsp;&emsp;如果发送缓冲区没有空间，则立即返回-1，errno为EWOULDBLOCK或EAGAIN，如果errno为别的值，则表明发送失败；如果空间不足以拷贝所有待发送数据或者空间足够的话，则拷贝前面N个能够容纳的数据，返回实际拷贝的字节数。  

| | 阻塞模式 | 非阻塞模式 |  
|:-|:---------|:-----------|
| 没有空间 | 阻塞 | 返回-1，errno为EWOULDBLOCK或EAGAIN |
| 空间不足 | 阻塞  | 拷贝前面N个能够容纳的数据，返回实际拷贝的字节数 |
| 空间足够 | 拷贝所有数据到发送缓冲区，然后返回实际拷贝的字节数 | 拷贝所有数据到发送缓冲区，然后返回实际拷贝的字节数 |  

***
## REUSEADDR  
一般来说，一个端口释放后会等待一段时间之后才能被再次使用，SO_REUSEADDR是让端口释放后立即就可以被再次使用。  
对于处于TIME_WAIT状态的TCP套接字，SO_REUSEADDR可以允许其重复绑定使用。  
如果一个进程创建了套接字，并绑定了一个端口，接收了若干连接，然后杀死该进程，由于没有调用close()关闭套接字，因此需要等待一段时间后才能重新使用这个端口。重新启动后，只有在bind()前指定了SO_REUSEADDR才能成功绑定相同的端口。  

***
## CLOSEONEXEC  
进程在调用fork()后，产生子进程，子进程在调用exec()执行新的程序时需要关闭已经打开的文件描述符，否则会导致文件描述符被子进程占用，如果被占用的文件描述符是socket fd，那么父进程在重新启动后，就无法正常使用这些文件描述符所绑定的端口；如果被占用的文件描述符代表普通文件，则子进程存在越权行为。  
使用以下方式设置文件描述符的FD_CLOEXEC标识位：  

    auto flags = fcntl(fd, F_GETFD);  
    flags |= FD_CLOEXEC;  
    fcntl(fd, F_SETFD, flags);  
