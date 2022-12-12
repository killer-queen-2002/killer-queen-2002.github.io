# 大作业步骤


## 1.连接git

![新建仓库](..\static\新建仓库.PNG)

如图所示，我用的是国内的gitee仓库。

![git连接](..\static\git连接.png)

如图所示，创建完仓库后在终端上输入以上命令即可。在创建完仓库后会给代码提示。

![git log](..\static\git log.PNG)

如图，上传后的git日志。

## 2.socket

客户端代码：

```
#include <iostream>
#include <unistd.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <string.h>
#include <errno.h>
 
using namespace std;
 
int main(int argc,char** argv)
{
	int clitfd;  //文件描述符
	struct sockaddr_in serv_addr;  //目的服务端地址结构体
 
	memset(&serv_addr,0,sizeof(serv_addr));
 
	if(argc!=3)
	{
		cout<<"Input error! Usage should be : "<<argv[0]<<"  xxx.xxx.xxx.xxx(ip)  1234(port)"<<endl;
		return 0;
	}
 
	if((clitfd = socket(AF_INET,SOCK_STREAM,0)) == -1)  //创建套接字
	{
		cout<<"creat socket failed : "<<strerror(errno)<<endl;
		return 0;
	}
        //将目的服务端的地址信息赋值给地址结构体
	serv_addr.sin_family = AF_INET;
	serv_addr.sin_port = htons(atoi(argv[2]));
	serv_addr.sin_addr.s_addr = inet_addr(argv[1]);
 
	cout<<"try to connect ... "<<endl;
 
        //通过套接字发起连接请求，成功后clitfd套接字则表示此次成功的连接
	if( connect(clitfd,(struct sockaddr*)& serv_addr,sizeof(serv_addr)) == -1)
	{
		cout<<"connet failed : "<<strerror(errno)<<endl;
		return 0;
	}
 
	cout<<"connect success !"<<endl;
 
	while(1)
	{
		char sdbuf[1024];
		char rvbuf[1024];
		int rdlen,sdlen,i=0;
		cout<<"(Client)send : ";
 
		while((sdbuf[i] = getchar()) != '\n')i++;
                if(i==0)continue; //防止客户端只发一个换行符，此时i=0，write不发送数据，服务端就不回回发数据，然后客户端就一直阻塞在read处。因此如果i=0，则直接重新输入
 
		sdlen = write(clitfd,sdbuf,i);  //向套接字中写入数据发送
        //可能会出现发送端把长度为sdlen的字符串分为多次发送，调用一次read就很有可能不能读取完全，就有以下两种方式进行读取：
        /*1. write了多少字节就读取多少字节长的字符串*/
		rdlen=0;
		while(rdlen<sdlen)//防止发送端将数据分开发送
		{	
			int rdcnt = read(clitfd,&rvbuf[rdlen],sizeof(rvbuf));
			if(rdcnt == -1)
			{
				perror(NULL);
				continue;
			}
			rdlen+=rdcnt;
		}
                if(rdlen)
                {
		    rvbuf[rdlen]='\0';
		    cout<<"(Client)recv : "<<rvbuf<<endl;
                }   
                else 
		{
	            cout<<"Server has closed ! "<<endl;
		    cout<<"Client will close..."<<endl;
		break;
		} 
 
 
        /*2. 用recv函数中的MSG_WAITALL参数，读到指定长度的数据才返回*/
        /*if(rdlen = recv(clitfd,&rvbuf[rdlen],sdlen,MSG_WAITALL))
		{	
			rvbuf[rdlen]='\0';
	
			cout<<"(Client)recv : "<<rvbuf<<endl;
		}
		else 
		{
			cout<<"Server has closed ! "<<endl;
			cout<<"Client will close..."<<endl;
			break;
		}*/
 
	}
 
	close(clitfd);
}
```

服务端代码：

```
#include <iostream>
#include <unistd.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <string.h>
#include <errno.h>
 
#define SERV_PORT 8888        //服务器端口
#define SERV_IP "127.1.1.1"   //服务器ip
 
using namespace std;
 
int main(int argc,char** argv)
{
	int servfd,clitfd;   //创建两个文件描述符，servfd为监听套接字，clitfd用于数据传输
	struct sockaddr_in serv_addr,clit_addr; //创建地址结构体，分别用来存放服务端和客户端的地址信息
 
	memset(&serv_addr,0,sizeof(serv_addr));  //初始化
	memset(&clit_addr,0,sizeof(clit_addr));  //初始化
 
	if((servfd = socket(AF_INET,SOCK_STREAM,0)) == -1)  //创建套接字
	{
		cout<<"creat socket failed : "<<strerror(errno)<<endl;//如果出错则打印错误
		return 0;
	}
        
        //给服务端的地址结构体赋值
	serv_addr.sin_family = AF_INET;
	serv_addr.sin_port = htons(SERV_PORT); //将主机上的小端字节序转换为网络传输的大端字节序（如果主机本身就是大端字节序就不用转换了）
	serv_addr.sin_addr.s_addr = inet_addr(SERV_IP); //将字符串形式的ip地址转换为点分十进制格式的ip地址
 
        //绑定地址信息到监听套接字上，第二个参数强转是因为形参类型为sockaddr ，而实参类型是sockaddr_in 型的
	if(bind(servfd,(sockaddr *)& serv_addr,sizeof(serv_addr)) == -1)
	{
		cout<<"bind failed : "<<strerror(errno)<<endl;
		return 0;
	}
        
        //将servfd套接字置为监听状态
	if(listen(servfd,1024) == -1)
	{
		cout<<"listen failed : "<<strerror(errno)<<endl;
		return 0;
	}
 
	cout<<"Init Success ! "<<endl;
	cout<<"ip : "<<inet_ntoa(serv_addr.sin_addr)<<"  port : "<<ntohs(serv_addr.sin_port)<<endl;
	cout<<"Waiting for connecting ... "<<endl;
 
	socklen_t clit_size = 0; //用于accept函数中保存客户端的地址结构体大小
 
        //accept成功后，clitfd则指向了这条服务端与客户端成功连接的”通路“
	if((clitfd = accept(servfd,(sockaddr *)& clit_addr,&clit_size)) == -1)
	{
		cout<<"accept failed : "<<strerror(errno)<<endl;
		return 0;
	}
 
	cout<<"Client access : "<<inet_ntoa(clit_addr.sin_addr)<<"  "<<ntohs(clit_addr.sin_port)<<endl;
 
 
	char buf[1024]; //用于读写数据
 
	while(1)
	{
		int rdstate;
		if((rdstate = read(clitfd,buf,sizeof(buf))) > 0 )//通过clitfd来读取数据，返回值为读取的长度
		{
			int i=0;
			cout<<"(Server)recv : ";
			for(i=0;i<rdstate;i++)
			{
				cout<<buf[i];
				buf[i] = toupper(buf[i]); //转换为大写
			}
			buf[i]='\0';
			cout<<endl;
			write(clitfd,buf,strlen(buf)); //发回客户端
		}
                else if(rdstate == 0)  //客户端退出
		{
			cout<<"client exit ! "<<endl;
			return 0;
		}
	}
 
	close(servfd);  //关闭套接字
	close(clitfd);  
	return 0;
}
```

![socket](..\static\socket.PNG)

如图，新建一个socket文件夹，在里面vim一个客户端代码文件和一个服务端代码文件，用g++编译运行后即可实现通讯，实现效果如下图：

![socket成功通信](..\static\socket成功通信.PNG)

## 3.Hugo

首先是安装hugo，用的代码如下：

```
wget   https://github.com/gohugoio/hugo/releases/download/v0.80.0/hugo_0.80.0_Linux-64bit.deb
```

下载完后用以下代码安装：

```
sudo dpkg -i hugo_0.80.0_Linux-64bit.deb
```

![hugo版本](..\static\hugo版本.PNG)

如图，验证hugo版本。

随后用hugo来生成博客，代码如下：

```
hugo new site myblog
```

![new blog效果图](..\static\new blog效果图.PNG)

随后在https://gohugo.io/网页下载主题：

![下载主题](..\static\下载主题.PNG)

下载完后放入博客文件夹的themes文件夹内，用hugo serve开启服务，即可在本地启动博客

![开启hugo serve](..\static\开启hugo serve.PNG)

![本地博客](..\static\本地博客.PNG)

用以下代码即可生成md文件：

```
hugo new post/xxx.md
```

