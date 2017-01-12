---
layout: post
title:8.【学习】Apache Mina
---

### 写在前边的废话

Mina框架忘得差不多了，重新复习了一下，跟第一次学习很大不同了。
唉，牛逼的人却更努力，想想都觉得自己这半年的强度也不过如此了。

### 理解
- Apache Mina，基于TCP、UDP的框架
- IoAcceptor 适配器，有请求时创建session，添加IoFilter过滤
- NioSocketAcceptor extends IoAcceptor用于TCP
- NioDatagramAcceptor extends IoAcceptor用于UDP
- IoAcceptor需要注册handler extends IoHandlerAdapter，重写方法实现事件驱动

### 服务器代码
```java

public class MinaServer {
	private static final int PORT = 8888;
	public static void main( String[] args ) throws IOException
	{
//	   NioSocketAcceptor acceptor = new NioSocketAcceptor();
//	   acceptor.getFilterChain().addLast( "logger", new LoggingFilter() );
//	   acceptor.getFilterChain().addLast( "codec", new ProtocolCodecFilter( new TextLineCodecFactory( Charset.forName( "UTF-8" ))));
//	   acceptor.setHandler(  new TimeServerHandler() );
//	   acceptor.getSessionConfig().setReadBufferSize( 2048 );
//	   acceptor.getSessionConfig().setIdleTime( IdleStatus.BOTH_IDLE, 10 );
//	   acceptor.bind( new InetSocketAddress(PORT) );
	   
		NioDatagramAcceptor udpAcceptor = new NioDatagramAcceptor();
		udpAcceptor.setHandler( new TimeServerHandler());
		udpAcceptor.getFilterChain().addLast("codec", new ProtocolCodecFilter(new TextLineCodecFactory( Charset.forName( "UTF-8" ))));
		
		DatagramSessionConfig dcfg = udpAcceptor.getSessionConfig();
		dcfg.setReadBufferSize(4096);//设置接收最大字节默认2048  
       dcfg.setReceiveBufferSize(4096);//设置输入缓冲区的大小  
       dcfg.setSendBufferSize(4096);//设置输出缓冲区的大小  
       dcfg.setReuseAddress(true);//设置每一个非主监听连接的端口可以重用  
       udpAcceptor.bind(new InetSocketAddress(PORT));
	}
}
```
_IoHandler略_

###netcat测试

- nc 127.0.0.1 8888
TCP 通过8888端口建立与服务器，之后发送消息到服务器

_通过WireShark监控，看到三次握手过程_

- nc -u 127.0.0.1 8888
UDP 通过8888端口直接发送封包

### 小结
- Mina使用上不困难，看项目中也没有很难理解的地方，只有过滤器涉及到了些解码编码的业务逻辑
- UDP没有长连接一说
- nc可以用来监听本地端口，nc -lp 8888(nc -lup 8888)可以实现本地通讯

_但man中有这样一句话_

```
-l      Used to specify that nc should listen for an incoming con-
             nection rather than initiate a connection to a remote host.
             It is an error to use this option in conjunction with the
             -p, -s, or -z options.  Additionally, any timeouts specified
             with the -w option are ignored.
```
_这我就不能理解了_



