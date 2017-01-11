---
layout: post
title:2.【学习】CountDownLatch
---


### 写在前边的废话：

昨晚，不今早打包到7点。想到明天要结束这次海南的封闭开发了，忍不住高兴一下

### CountDownLatch

#### 描述

同步辅助类，可以让线程等待至一组线程结束时执行。

#### 主要方法

- public void countDownLatch(int count); 被计数线程数量
- public void countDown(); 供被计数线程调用
- public void await() throws InterruptedException 等待线程调用，latch > 0 阻塞

#### 代码
```java 
import java.util.concurrent.CountDownLatch;

public class TestCountDownLatch{
	public static void main(String[] args) {
		CountDownLatch latch = new CountDownLatch(2);
		new Thread1(latch).start();
		for(int i=0;i<5;i++){
			new Thread2(latch).start();
		}
	}
	
	static class Thread1 extends Thread{
		private CountDownLatch latch;
		public Thread1(CountDownLatch latch) {
			this.latch = latch;
		}
		@Override
		public void run() {
			try {
				latch.await();
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			System.out.println("thread1");
		}
	}
	static class Thread2 extends Thread{
		private CountDownLatch latch;
		public Thread2(CountDownLatch latch){
			this.latch = latch;
		}
		@Override
		public void run() {
			System.out.println("thread2");
			latch.countDown();
		}
	}
}

```

### static class

写这个测试的时候发现了不使用静态类无法调用，又回去补了一下静态类的概念，总结一下

- 非静态内部类不能声明静态方法，静态成员需要用final修饰 
- 静态内部类只能调用外部静态方法或者成员变量
- 创建静态内部类不需要将实例绑定在外部的实例上

基于最后一条，使用非静态内部类时，启动线程可以写成

```java
new TestCountDownLatch().new Thread1(latch).start();

```

