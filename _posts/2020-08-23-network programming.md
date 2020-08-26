# 网络编程

## **socket API**  
### **socket**  
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
### **bind**  
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
### **listen**  
API 原型：  
&emsp;&emsp;int listen(int sockfd, int backlog);  
作用：  
&emsp;&emsp;将sockfd标识的套接字描述符状态从CLOSED转换为LISTEN，同时设置已连接队列里等待accept()取走的连接的最大数目  
返回值：  
&emsp;&emsp;正确：0  
&emsp;&emsp;错误：-1，可以通过WSAGetLastError() 获取相应错误代码  
参数：  
&emsp;&emsp;int sockfd：用于标识一个已绑定但未连接的套接字描述符  
&emsp;&emsp;int backlog：已连接队列里等待accept()取走的连接的最大数目  
头文件：  
&emsp;&emsp;sys/socket.h  

***
### **connect**  
API 原型：  
&emsp;&emsp;int connect(int sockfd, struct sockaddr* serv_addr, socklen_t addrlen);  
作用：  
&emsp;&emsp;用于建立与指定地址的连接  
返回值：  
&emsp;&emsp;正确：0  
&emsp;&emsp;错误：SOCKET_ERROR，可以通过WSAGetLastError() 获取相应错误代码  
参数：  
&emsp;&emsp;int sockfd: 客户端套接字描述符  
&emsp;&emsp;struct sockaddr * serv_addr: 服务端主机IP地址和端口号，客户端填写后传入  
&emsp;&emsp;socklen_t addrlen: 结构体sockaddr的长度  
头文件：  
&emsp;&emsp;sys/socket.h  

***
### **accept**  
API 原型：  
&emsp;&emsp;int accept(int sockfd, struct sockaddr* addr, socklen_t* addrlen);  
作用：  
&emsp;&emsp;接受客户端的连接  
返回值：  
&emsp;&emsp;正确：一个新分配的文件描述符，用于标识建立的连接  
&emsp;&emsp;错误：INVALID_SOCKET，可以通过WSAGetLastError() 获取相应错误代码  
参数：  
&emsp;&emsp;int sockfd: 服务端套接字描述符，该套接字描述符在listen()后监听连接请求  
&emsp;&emsp;struct sockaddr* addr: 客户端主机IP地址和端口号，由内核填写  
&emsp;&emsp;socklen_t addrlen: 结构体sockaddr的长度  
头文件：  
&emsp;&emsp;sys/socket.h  

***
### **write**  
API 原型：  
&emsp;&emsp;ssize_t write(int sockfd, const void* buf, size_t len);  
作用：  
&emsp;&emsp;向一个已连接的套接字描述符发送数据  
返回值：  
&emsp;&emsp;正确：成功发送的字节数  
&emsp;&emsp;错误：-1，并设置errno  
参数：  
&emsp;&emsp;int sockfd:   
&emsp;&emsp;&emsp;&emsp;客户端：客户端套接字描述符  
&emsp;&emsp;&emsp;&emsp;服务端：连接套接字描述符  
&emsp;&emsp;const void* buf: 包含待发送数据的缓冲区  
&emsp;&emsp;size_t len: 缓冲区中数据的长度  
头文件：  
&emsp;&emsp;unistd.h  

***
### **read**  
API 原型：  
&emsp;&emsp;int read(int sockfd, void* buf, size_t len);  
作用：  
&emsp;&emsp;接收数据  
返回值：  
&emsp;&emsp;正确：  
&emsp;&emsp;&emsp;&emsp;连接正常：实际拷贝到buf中的字节数  
&emsp;&emsp;&emsp;&emsp;连接关闭：0  
&emsp;&emsp;错误：-1，并设置errno  
参数：  
&emsp;&emsp;int sockfd:   
&emsp;&emsp;&emsp;&emsp;客户端：客户端套接字描述符  
&emsp;&emsp;&emsp;&emsp;服务端：连接套接字描述符  
&emsp;&emsp;void* buf: 接收数据的缓冲区  
&emsp;&emsp;size_t len: buf的长度  
头文件：  
&emsp;&emsp;unistd.h  

***
### **close**  
API 原型：  
&emsp;&emsp;int close(int fd);  
作用：  
&emsp;&emsp;关闭指定的文件描述符  
返回值：  
&emsp;&emsp;正确：0  
&emsp;&emsp;错误：-1并设置errno  
参数：  
&emsp;&emsp;int fd: 要关闭的描述符  
头文件：  
&emsp;&emsp;unistd.h  

***
### **fcntl**  
API 原型：  
&emsp;&emsp;int fcntl(int fd, int cmd, long arg);  
作用：  
&emsp;&emsp;用于操作文件描述符的一些特性  
返回值：  
&emsp;&emsp;正确：与命令有关  
&emsp;&emsp;&emsp;&emsp;F_GETFL：返回相应标志  
&emsp;&emsp;错误：-1  
参数：  
&emsp;&emsp;int fd：待操作的文件描述符  
&emsp;&emsp;int cmd：待操作的命令  
&emsp;&emsp;&emsp;&emsp;F_GETFL：取得文件描述符状态旗标  
&emsp;&emsp;&emsp;&emsp;F_SETFL：设置文件描述符状态旗标  
&emsp;&emsp;long arg：待操作的命令的参数  
头文件：  
&emsp;&emsp;fcntl.h    

***
### **setsockopt**  
API 原型：  
&emsp;&emsp;int setsockopt(int sockfd, int level, int optname, const void* optval, socklen_t optlen);  
作用：  
&emsp;&emsp;设置套接字的选项  
返回值：  
&emsp;&emsp;正确：0  
&emsp;&emsp;错误：SOCKET_ERROR，可以通过WSAGetLastError() 获取相应错误代码  
参数：  
&emsp;&emsp;int sockfd: 要设置选项的套接字描述符  
&emsp;&emsp;int level: 选项定义的层次  
&emsp;&emsp;int optname: 选项名  
&emsp;&emsp;const void* optval: 选项值  
&emsp;&emsp;socklen_t optlen: optval缓冲区长度  
头文件：  
&emsp;&emsp;sys/socket.h  
&emsp;&emsp;sys/types.h  

***  
## **TCP状态转换**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/TCP1.png">
</div>  

## **IO模型**  
IO分为两个阶段：  
1.	数据准备阶段  
2.	内核空间拷贝数据到用户空间阶段  

### **阻塞IO**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/blocking IO.png">
</div>

### **非阻塞IO**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/nonblocking IO.png">
</div>

### **IO多路复用**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/IO multiplexing.png">
</div>

### **信号驱动IO**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/signal-driven IO.png">
</div>

### **异步IO**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/async IO.png">
</div>

### **五种IO模型对比**  
<div align="center">
    <img src="https://maopeilin1986.github.io/assets/images/five IO model.png">
</div>

阻塞IO和非阻塞IO的区别在于第一阶段发起IO请求是否会被阻塞  
同步IO和异步IO的区别在于第二阶段是否会被阻塞  
阻塞IO模型、非阻塞IO模型、IO复用模型、信号驱动IO模型都属于同步IO  

### **阻塞与同步**  
阻塞可以是实现同步的一种手段，同步是两个对象之间的关系，而阻塞是一个对象的状态。  


### **四种组合方式**   
#### **同步阻塞方式**   
发送方发送请求之后一直等待响应。  
接收方处理请求时进行的IO操作如果不能马上等到返回结果，就一直等到返回结果后，才响应发送方，期间不能进行其他工作。  
#### **同步非阻塞方式**  
发送方发送请求之后，一直等待响应。  
接受方处理请求时进行的IO操作如果不能马上得到结果，就立即返回，去做其他事情。  
但是由于没有得到请求处理结果，不响应发送方，发送方一直等待。  
当IO操作完成以后，将完成状态和结果通知接收方，接收方再响应发送方，发送方才进入下一次请求过程。（实际不应用）  
#### **异步阻塞方式**  
发送方向接收方请求后，不等待响应，可以继续其他工作。  
接收方处理请求时进行IO操作如果不能马上得到结果，就一直等到返回结果后，才响应发送方，期间不能进行其他操作。（实际不应用）  
#### **异步非阻塞方式**  
发送方向接收方请求后，不等待响应，可以继续其他工作。  
接收方处理请求时进行IO操作如果不能马上得到结果，也不等待，而是马上返回去做其他事情。  
当IO操作完成以后，将完成状态和结果通知接收方，接收方再响应发送方。（效率最高）  
### **扩展**  
#### **同步调用与异步调用**  
同步调用就是调用一但返回，就能知道结果，而异步是返回时不一定知道结果，还得通过其他机制来获知结果，如：a. 状态 b. 通知 c. 回调函数  
#### **同步线程与异步线程**  
同步线程：即两个线程步调要一致，其中一个线程可能要阻塞等待另外一个线程的运行，要相互协商。快的阻塞一下等到慢的步调一致。  
异步线程：步调不用一致，各自按各自的步调运行，不受另一个线程的影响。  
#### **同步通信与异步通信**   
同步通信是指：发送方和接收方通过一定机制，实现收发步调协调。如：发送方发出数据后，等接收方发回响应以后才发下一个数据包的通讯方式。  
异步通信是指：发送方的发送不管接收方的接收状态。如：发送方发出数据后，不等接收方发回响应，接着发送下个数据包的通讯方式。  
## **IO多路复用API**  
###	**select**  
#### **FD_ZERO**  
API 原型：  
&emsp;&emsp;void FD_ZERO (fd_set* fdset);  
作用：  
&emsp;&emsp;清空集合  
返回值：  
&emsp;&emsp;无  
参数：  
&emsp;&emsp;fd_set* fdset：文件描述符集合  
头文件：  
&emsp;&emsp;sys/select.h  

***
#### **FD_SET**  
API 原型：  
&emsp;&emsp;void FD_SET (int fd, fd_set* fdset);  
作用：  
&emsp;&emsp;将指定的文件描述符加入到集合中  
返回值：  
&emsp;&emsp;无  
参数：  
&emsp;&emsp;int fd：指定的文件描述符  
&emsp;&emsp;fd_set* fdset：文件描述符集合  
头文件：  
&emsp;&emsp;sys/select.h  

***
#### **FD_CLR**  
API 原型：  
&emsp;&emsp;void FD_CLR (int fd, fd_set* fdset);  
作用：  
&emsp;&emsp;将指定的文件描述符从集合中删除  
返回值：  
&emsp;&emsp;无  
参数：  
&emsp;&emsp;int fd：指定的文件描述符  
&emsp;&emsp;fd_set* fdset：文件描述符集合  
头文件：  
&emsp;&emsp;sys/select.h  

***
#### **FD_ISSET**  
API 原型：   
&emsp;&emsp;int FD_ISSET (int fd, fd_set* fdset);   
作用：   
&emsp;&emsp;检查指定的文件描述符是否在集合中（是否可以读写）   
返回值：   
&emsp;&emsp;>0：指定的文件描述符在集合中   
&emsp;&emsp;0：指定的文件描述符不在集合中   
参数：   
&emsp;&emsp;int fd：指定的文件描述符   
&emsp;&emsp;fd_set* fdset：文件描述符集合   
头文件：   
&emsp;&emsp;sys/select.h   

***
#### **select**  
API 原型：  
&emsp;&emsp;int select (int maxfd, fd_set* readset, fd_set* writeset, fd_set* exceptset, const timeval* timeout);  
作用：  
&emsp;&emsp;监视文件描述符的变化情况——读写或是异常  
返回值：  
&emsp;&emsp;正确：  
&emsp;&emsp;&emsp;&emsp;>0：就绪文件描述符的数目  
&emsp;&emsp;&emsp;&emsp;0：超时  
&emsp;&emsp;错误：  
&emsp;&emsp;&emsp;&emsp;-1：出错  
参数：  
&emsp;&emsp;int maxfd：最大的文件描述符  
&emsp;&emsp;fd_set* readset：检测可读的文件描述符集合  
&emsp;&emsp;fd_set* writeset：检测可写的文件描述符集合  
&emsp;&emsp;fd_set* exceptset：检测异常的文件描述符集合  
&emsp;&emsp;const timeval* timeout：阻塞时间，有三种情况：  
&emsp;&emsp;&emsp;&emsp;NULL：一直阻塞，直到某个文件描述符的状态发生变化    
&emsp;&emsp;&emsp;&emsp;0：非阻塞，立即返回，无论是否有文件描述符的状态发生变化    
&emsp;&emsp;&emsp;&emsp;>0：阻塞指定超时时间，在超时时间之内如果有文件描述符的状态发生变化则立即返回，否则直到超时返回    
头文件：  
&emsp;&emsp;sys/select.h  

***
### **poll**  
API 原型：  
&emsp;&emsp;int poll ( struct pollfd* fds, unsigned int nfds, int timeout);  
作用：  
&emsp;&emsp;监视文件描述符的变化情况——读写或是异常  
返回值：  
&emsp;&emsp;正确：  
&emsp;&emsp;&emsp;&emsp;>0：数组fds中准备好读、写或出错状态的文件描述符总数  
&emsp;&emsp;&emsp;&emsp;0：数组fds中没有任何文件描述符准备好读、写或出错  
&emsp;&emsp;错误：  
&emsp;&emsp;&emsp;&emsp;-1：出错，并设置errno  
参数：  
&emsp;&emsp;struct pollfd* fds：结构体pollfd数组指针  
&emsp;&emsp;unsigned int nfds：结构体pollfd数组中元素的总数  
&emsp;&emsp;int timeout：阻塞时间  
头文件：  
&emsp;&emsp;poll.h  
相关结构体定义：  
&emsp;&emsp;struct pollfd {  
&emsp;&emsp;&emsp;&emsp;int fd;			//文件描述符  
&emsp;&emsp;&emsp;&emsp;short events;		//监视文件描述符的事件掩码，由用户填写  
&emsp;&emsp;&emsp;&emsp;short revents;		//文件描述符的操作结果事件掩码，由内核填写  
&emsp;&emsp;}  

***
### **epoll**  
#### **epoll_create**  
API 原型：  
&emsp;&emsp;int epoll_create(int size);  
作用：  
&emsp;&emsp;创建一个epoll实例  
返回值：  
&emsp;&emsp;正确：  
&emsp;&emsp;&emsp;&emsp;>=0：新的epoll实例的文件描述符  
&emsp;&emsp;错误：  
&emsp;&emsp;&emsp;&emsp;-1：并设置errno  
参数：  
&emsp;&emsp;int size：文件描述符的个数，linux内核版本大于2.6.8后，该参数弃用，但必须大于0  
头文件：  
&emsp;&emsp;sys/epoll.h    

***
#### **epoll_ctl**  
API 原型：  
&emsp;&emsp;int epoll_ctl(int epfd, int op, int fd, struct epoll_event* event);  
作用：  
&emsp;&emsp;添加、修改、删除需要监视的文件描述符及其事件  
返回值：  
&emsp;&emsp;正确：0  
&emsp;&emsp;错误：-1并设置errno  
参数：  
&emsp;&emsp;int epfd：epoll实例的文件描述符  
&emsp;&emsp;int op：操作类型  
&emsp;&emsp;&emsp;&emsp;EPOLL_CTL_ADD：注册fd，并将fd和event联系起来  
&emsp;&emsp;&emsp;&emsp;EPOLL_CTL_MOD：改变fd和event之间的联系  
&emsp;&emsp;&emsp;&emsp;EPOLL_CTL_DEL：注销fd，event可以为空  
&emsp;&emsp;int fd：监视的文件描述符  
&emsp;&emsp;struct epoll_event* event：epoll事件  
头文件：  
&emsp;&emsp;sys/epoll.h  
相关结构体定义：  
&emsp;&emsp;struct epoll_event {  
&emsp;&emsp;&emsp;&emsp;uint32_t events;		//epoll事件掩码 EPOLLIN | EPOLLOUT | EPOLLET  
&emsp;&emsp;&emsp;&emsp;epoll_data_t data;		//用户数据  
&emsp;&emsp;};  
&emsp;&emsp;typedef union epoll_data {  
&emsp;&emsp;&emsp;&emsp;void* ptr;				//用户自定义数据  
&emsp;&emsp;&emsp;&emsp;int fd;				//监视的文件描述符  
&emsp;&emsp;&emsp;&emsp;uint32_t u32;			//32位用户自定义数据  
&emsp;&emsp;&emsp;&emsp;uint64_t u64;			//64位用户自定义数据  
&emsp;&emsp;} epoll_data_t;  

***
#### **epoll_wait**  
API 原型：  
&emsp;&emsp;int epoll_wait(int epfd, struct epoll_event* events, int maxevents, int timeout);  
作用：  
&emsp;&emsp;等待epoll事件  
返回值：  
&emsp;&emsp;正确：  
&emsp;&emsp;&emsp;&emsp;>0：events数组中就绪的文件描述符总数  
&emsp;&emsp;&emsp;&emsp;0：events数组中没有任何文件描述符就绪  
&emsp;&emsp;错误：  
&emsp;&emsp;&emsp;&emsp;-1：出错，并设置errno  
参数：  
&emsp;&emsp;int epfd：epoll实例的文件描述符  
&emsp;&emsp;struct epoll_event* events：epoll_event结构体数组  
&emsp;&emsp;int maxevents：events数组中的元素个数  
&emsp;&emsp;int timeout：阻塞超时时间  
&emsp;&emsp;&emsp;&emsp;-1：一直阻塞，直到某个文件描述符的状态发生变化  
&emsp;&emsp;&emsp;&emsp;0：非阻塞，立刻返回，无论是否有文件描述符的状态发生变化  
&emsp;&emsp;&emsp;&emsp;>0: 阻塞指定超时时间，在超时时间之内如果有文件描述符的状态发生变化则立即返回，否则直到超时返回  
头文件：  
&emsp;&emsp;sys/epoll.h  
扩展：  
&emsp;&emsp;epoll的两种工作模式：  
&emsp;&emsp;epoll有两种工作模式：LT模式（Level Trigger水平触发）和ET模式（Edge Trigger边缘触发）。默认情况下，epoll采用 LT模式工作，这时可以处理阻塞和非阻塞套接字，ET模式的效率要比 LT模式高，它只支持非阻塞套接字。ET模式仅当状态发生变化的时候才获得通知,这里所谓的状态的变化并不包括缓冲区中还有未处理的数据,也就是说,如果要采用ET模式,需要一直read/write直到出错为止。而LT模式是只要有数据没有处理就会一直通知下去。  

***
## **文件描述符的非阻塞模式**  

### **connect**  
涉及fd：  
&emsp;&emsp;客户端套接字文件描述符client_socket_fd。  
阻塞模式：  
&emsp;&emsp;客户端调用connect()会触发TCP的三次握手过程，仅在连接建立成功或出错时才返回。  
非阻塞模式：  
&emsp;&emsp;客户端调用connect()，如果返回0，则连接建立成功；如果返回-1，则检查errno，如果值为EINPROGRESS，则连接正在建立。为了控制连接的建立时间，将该socket   fd加入到epoll的监控事件集合中，如果超时时间内连接成功建立，则该socket fd变为可写，否则该socket fd变为既可读又可写，此时调用getsockopt()，当errno等于0时表示只可写。  

***
### **listen**  
涉及fd：  
&emsp;&emsp;服务端套接字文件描述符(监听套接字文件描述符)listen_socket_fd。  
阻塞模式或非阻塞模式对于listen()调用是没有区别的，因为listen调用仅仅做两件事：  
&emsp;&emsp;当调用socket()创建一个套接字时，默认创建一个主动套接字，即它是一个将调用connect()发起连接的客户端套接字。listen()调用把一个未连接的套接字转换为一个被动套接字，指示内核应该接受指向该套接字的连接请求。根据TCP状态转换图，调用listen()将导致套接字从CLOSED状态转换为LISTEN状态；  
&emsp;&emsp;使用第二个参数设置套接字队列的最大长度。  

***
### **accept**  
涉及fd：  
&emsp;&emsp;服务端套接字文件描述符(监听套接字文件描述符)listen_socket_fd。  
阻塞模式：  
&emsp;&emsp;客户端发起连接，完成三次握手之后，在服务端还没有调用accept()之前，客户端就调用close()关闭连接，此时客户端发送的是RST报文而不是FIN报文，所以该连接在内核中就被断开了，但是此时用户层并不知道，如果此时服务端经过epoll_wait()后，调用accept()，由于连接已经断开，因此accept()调用会一直阻塞。  
非阻塞模式：  
&emsp;&emsp;如果有连接存在，则accept()正常返回；如果没有连接存在，或者连接被断开了，则accept()返回不同的错误码，此时属于正常情况，不用处理。  

***
### **epoll_wait**  
涉及fd：  
&emsp;&emsp;客户端套接字文件描述符client_socket_fd；  
&emsp;&emsp;服务端套接字文件描述符(监听套接字文件描述符)listen_socket_fd；  
&emsp;&emsp;连接文件描述符connection_fd。  
阻塞模式：  
&emsp;&emsp;只支持EPOLLLT模式。  
非阻塞模式：  
&emsp;&emsp;支持EPOLLET和EPOLLLT模式。  

***
### **read**  
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
### **write**  
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
## **REUSEADDR**  
一般来说，一个端口释放后会等待一段时间之后才能被再次使用，SO_REUSEADDR是让端口释放后立即就可以被再次使用。  
对于处于TIME_WAIT状态的TCP套接字，SO_REUSEADDR可以允许其重复绑定使用。  
如果一个进程创建了套接字，并绑定了一个端口，接收了若干连接，然后杀死该进程，由于没有调用close()关闭套接字，因此需要等待一段时间后才能重新使用这个端口。重新启动后，只有在bind()前指定了SO_REUSEADDR才能成功绑定相同的端口。  

***
## **CLOSEONEXEC**  
进程在调用fork()后，产生子进程，子进程在调用exec()执行新的程序时需要关闭已经打开的文件描述符，否则会导致文件描述符被子进程占用，如果被占用的文件描述符是socket fd，那么父进程在重新启动后，就无法正常使用这些文件描述符所绑定的端口；如果被占用的文件描述符代表普通文件，则子进程存在越权行为。  
使用以下方式设置文件描述符的FD_CLOEXEC标识位：  
<pre class='cpp'><code>
    auto flags = fcntl(fd, F_GETFD);  
    flags |= FD_CLOEXEC;  
    fcntl(fd, F_SETFD, flags);  
</code></pre>