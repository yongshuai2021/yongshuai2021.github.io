# c语言简明教程

## 基础

1. 指针
2. 引用
3. 函数
4. 宏定义
5. 存储空间  
   动态内存分配

    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>

    int main()
    {
        char name[100];
        char *description;
        
        strcpy(name, "Zara Ali");
        
        /* 动态分配内存 */
        description = (char *)malloc( 30 * sizeof(char) );
        if( description == NULL )
        {
            fprintf(stderr, "Error - unable to allocate required memory\n");
        }
        else
        {
            strcpy( description, "Zara ali a DPS student.");
        }
        /* 假设您想要存储更大的描述信息 */
        description = (char *) realloc( description, 100 * sizeof(char) );
        if( description == NULL )
        {
            fprintf(stderr, "Error - unable to allocate required memory\n");
        }
        else
        {
            strcat( description, "She is in class 10th");
        }
        
        printf("Name = %s\n", name );
        printf("Description: %s\n", description );
        
        /* 使用 free() 函数释放内存 */
        free(description);

        return 0;
    }

    ```

## 字符串操作

## 算法

1. 链表排序
2. 组合数

## io操作

1. 文件io  
   FILE *fopen( const char* filename, const char *mode );  
int fclose( FILE*fp );  
int fputc( int c, FILE *fp );  
int fputs( const char*s, FILE *fp );  
int fprintf(FILE*fp,const char *format, ...);  
int fgetc( FILE*fp );  
char *fgets( char* buf, int n, FILE*fp );

2. 终端io
3. 收发io操作
4. io复用  
   io多路复用就是通过一种机制，可以监视多个描述符，一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。  
   同步：阻塞I/O模型、非阻塞I/O模型、I/O复用(select 和 poll)模型、信号驱动I/O模型  
   异步：异步I/O(POSIX的aio_系列函数)模型

   函数|描述
   --|:--:|
   int select (int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, const struct timeval *timeout); | 位于<sys/select.h>; 功能：该函数允许进程指示内核等待多个事件中的任何一个发生，并只在一个或多个事件发生或经历一段指定的时间后才唤醒它。描述符不局限于套接字，任何描述符都可以使用select。返回：若有就绪描述符则为其数目，若超时则为0，若出错则为-1。|
   int pselect(int maxfdp1, fd_set *readset, fd_set *writeset, fd_Set *exceptset, const struct timespec *timeout, const sigset_t *sigmask); | 位于<sys/select.h>; 功能：类似于select，但比select更灵活; 返回：若有就绪描述符则为其数目，若超时则为0，若出错则为-1。|
   int poll(struct pollfd *fdarray, unsigned long nfds, int timeout); | 位于<pool.h>; 功能：类似于select，但是在处理流设备时提供了额外信息; 返回：若有就绪描述符则为其数目，若超时则为0，若出错则为-1。|

    ```c

    void echo(int connfd)
    {
        int n;
        char buf[MAXLINE];
        rio_t rio;
        
        rio_readinitb(&rio,connfd);
        //带缓冲的读取函数
        while((n=rio_readlineb(&rio,buf,MAXLINE))>0) {
            //向连接符写入内容
            printf("server received %d bytes \n",n);
            rio_writen(connfd,buf,n);
        }
    }
    
    /*command是作为键盘输入时执行的驱动动作*/
    void command(void) {
        char buf[MAXLINE];
        printf("you input just now!\n");
        //从标准输入中读取输入到buf中存储
        if(!fgets(buf,MAXLINE,stdin))
            exit(0);
        //输出buf中的数据
        printf("%s",buf);
    }

    int main(int argc,char **argv)
    {	
        //监听符，连接符，端口号
        int listenfd,connfd,port;
        //套接字地址结构的大小
        socklen_t clientlen=sizeof(struct sockaddr_in);
        //新建套接字地址结构
        struct sockaddr_in clientaddr;
        //fd_set为描述符集合，此处定义了两个read_set,ready_set描述符集合,分别是读集合/准备好集合
        fd_set read_set,ready_set;
        
        //如果运行时参数小于2，则提示错误
        if(argc!=2) {
            fprintf(stderr,"usage :%s <port>\n",argv[0]);
            exit(0);
        }
    
        //将第二个参数转化为整型端口号，args to integer
        port=atoi(argv[1]);
    
        //打开端口号，返回监听描述符
        listenfd=open_listenfd(port);
        
        //清空读集合
        FD_ZERO(&read_set);
        
        //将标准输入加到读集合
        FD_SET(STDIN_FILENO,&read_set);
    
        //将监听描述符加到读集合
        FD_SET(listenfd,&read_set);
    
        //服务器监听处理主程序
        while(1) {
    
            //将读集合赋值给准备好集合
            ready_set=read_set;
    
            //select函数会要求内核挂起进程，等待一个或多个IO事件发生后，才将控制返回给应用程序
            select(listenfd+1,&ready_set,NULL,NULL,NULL);
    
            //有IO事件后，将判断是来自从键盘上键入命令还是从客户端发来的请求，分别给出不同的回应
            if(FD_ISSET(STDIN_FILENO,&ready_set))
                command();
            if(FD_ISSET(listenfd,&ready_set)) {
                connfd=accept(listenfd,(SA *)&clientaddr,&clientlen);
                printf("client connected!");
                //向连接符回送数据
                echo(connfd);
                //关闭连接符，释放资源
                close(connfd);
            }
        }
    }

    ```
   
## 编码

1. 汉字GBK编码基础
2. ASCII编码
3. 整数编码

## 高级

1. 线程安全
2. 信号
3. 网络编程
4. 系统编程
5. 缓冲区溢出