### 多路复用IO模型

- 首先解决高并发的方案：

    多线程=>CPU上下文切换，繁琐

    单线程=> DMA控制器(cpu会将io操作交给dma进行操作，io结束后dma触发中断回馈给cpu，因为CPU的总线有多条线路，所以dma可以利用这些线路进行多线路的io操作)

    Linux中所有的内容都是一个文件，例如一个链接，可以是一个fd开头的文件

- 处理方案一：

    加入说有一百个链接，就是有一百个文件，我们单线程要维护一百个文件的变化的话，就需要一个死循环不停的去轮询所有的链接，也就是所有的文件，每次需要轮询一百个

    select函数

    ​	----> select(maxlink+1,&rset,NULL,NULL,NULL)

    ​					最大链接数+1 读文件描述符集合，写文件描述符集合，异常描述符集合，超时时间

    ​										文件描述符集合类型bitmap(01100101010) 有1的表示第几个文件描述符被占用

    ​										例如上面的表示1，2，5，7，9文件描述符被占用

    ​										默认1024位

    rset会bitmap会从用户态拷贝到内核态中进行轮训判断，如果一直没有数据就一直轮询进入阻塞状态，如果设置超时时间会跳出

    如果有数据：1.内核会将fd置位（标识为有数据，标识仅在bitmap中，此时数据在内核态中，fd无法标识）此时select不再阻塞

    2.select返回，返回之后根据bitmap去判断哪个元素被set，将被置位的数据读出进一步处理

    **弊端：** 

    ```c
    sockfd = socket(AF_INET,SOCK_STREAM,0);
    memset(&add,0,sizeof(addr));
    addr.sin_family = AF_INET;
    addr.sin_port = hotons(2000);
    addr.sin_addr.s_addr = INADDR_ANY;
    bind(sockfd,(struct sockaddr*)&addr ,sizeof(addr));
    listen(sockfd , 5);
    for(i = 0 ; i < 5 ;i++){
    	memset(&client0,sizeof(client));
    	addrlen = sizeof(client);
    	fds[i] = accept(sockfd,(struct sockaddr*)&client ,&addrlen);
    	if(fds[i] > max)
    		max = fds[i];
    }
    
    while(1){
    	FD_ZERO(&rset);
    	for(i = 0 ; i < 5; i++){
    		FD_SET(fds[i],&rset);
    	}
    	puts("round again");
    	select(max+1,&rset,NULL,NULL,NULL);<<<<<<<<<<<<<<<<<<<<<<<<
    	for(i = 0;i < 5 ; i++){
    		if(FD_ISSET(fds[i],&rest)){
    			memset(buffer,0,MAXBUF);
    			read(fds[i],buffer,MAXBUF);
    			put(buffer);
    		}
    	}
    }
    ```

    1. select传入内核中判断有数据的描述符后会进行标记，所以rset在select发现标记位后，rset每次都需要重新使用fds进行标记
    2. bitmap的标记位有1024个，所以只能标记1024个
    3. 用户态切换内核态开销
    4. 标记之后的rset需要遍历得知有数据的描述符是哪几个

- 方案二 poll：

    poll参数列表：poll(pollfds,5,5000)

    ​				pollfd的数组

    pollfd 结构体：

    ​		fd:fd	events:事件（read || write || read&write）revents: 对事件的一个回馈

    poll中向内核态中传入的是pollfd结构体，里面有revents字段用户标记

    poll方法也是阻塞进程，在有数据的时候会返回

    之后进行遍历，遍历找到置位点之后将revents重新标记为0

    ```c
    for (i = 0; i < 5; i++){
    	memset(&client,0,sizeof(client));
    	addrlen = sizeof(client);
    	pollfds[i] = accept(sockfd,(struct sockaddr*)&client ,&addrlen);
    	pollfds[i].events = POLLIN;
    }
    sleep(1);
    while(1){
    	puts("round again");
    	poll(pollfds,5,50000);<<<<<<<<<<<<<<<<<<<<<<<<<<
    	for(i=0;i<5;i++){
    		if(pollfds[i].revents&POLLIN){
    			pollfds[i].revents=0;
    			memset(buffer,0,MAXBUF);
    			read(pollfds[i].fd,buffer,MAXBUF);
    			puts(buffer);
    		}
    	}
    }
    ```

    相对于select的优点：

    ​	避免了置位之后的重新选位

    ​	最多可以维护的链接大于1024

- epoll

    ```c
    struct epoll_event events[5];
    int epfd = epoll_create(10);
    ...
    ...
    for(i = 0; i < 5; i++){
    	static struct epoll_event ev;
    	memset(&client,0,sizeof(client));
    	addrlen = sizeof(client);
    	ev.data.fd = accept(sockfd,(struct sockaddr*)&client ,&addrlen);
    	ev.events = EPOLLIN;
    	epoll_ctl(epfd,EPOLL_CTL_ADD,ev,data,fd,&ev);
    }
    while(1){
    	puts("round again");
    	nfds = epoll_wait(epfd,events,5,10000);<<<<<<<<<<<<<<<<<<
    	for (int i = 0; i < nfds; ++i)
    	{
    		memset(buffer,0,MAXBUF);
    		read(events[i],data.fd,buffer,MAXBUF);
    		puts(buffer);
    	}
    }
    ```

    epoll：首先也是阻塞到epoll_wait方法， 有数据后会返回一个int，标示有几个标识符有数据，并会将有数据的标识符放到前面的位置，因此就可以只循环有数据的标识符

    避免了重新选位

    ​	可维护标识符的有限性

    ​	避免了多余次数的遍历寻找置位位置

    ​	用户态和系统态的数据共享，避免了拷贝

