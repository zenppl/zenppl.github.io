---
layout: post
title:7.【学习】addShutDownHook使用
---

### 写在前边的废话

最近清闲，读了下以前没怎么读懂的启动入口，发现了这么个方法。

### 用处
在启动入口中调用，用来重启(退出)时结束已经随系统启动的线程。

### 作用

```java
void java.lang.Runtime.addShutdownHook(Thread hook)

```
用来在jvm中增加一个关闭的钩子。jvm被关闭时才会执行hook线程。

### Test


```java

public class TestAddShutdownHook {
	public static void main(String[] args) {
		Runtime.getRuntime().addShutdownHook(new Thread(new Runnable() {
			public void run() {
				System.out.println("t");
			}
		}));
		Thread t1 = new Thread(new Runnable() {
			public void run() {
				System.out.println("t1");
			}
		});
		Thread t2 = new Thread(new Runnable() {
			public void run() {
				System.out.println("t2");
			}
		});
		t1.start();
		t2.start();
		try {
			Thread.sleep(1000l);
			} 
		catch (Exception e) {
			
		}
	}
}

```

```
t1
t2
t

```