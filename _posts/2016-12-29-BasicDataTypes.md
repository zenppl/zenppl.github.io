---
layout: post
title:6.【小坑】基本数据类型运算
---

### 写在前边的废话

昨天遇到的问题，没想过这种面试题一般的问题真的会出现在代码中。。

### 情景
mvp判断bug，均为byte型但返回false

```java
mvpIndex.contains(battlePlayerData.getPlayerIndex() - (byte)1)

```


### Test


```java
public class TestByte {
		public static void main(String[] args) {	
		byte a = (byte) 2 -(byte) 1;
		byte b = 2-1;
		
		byte c = (byte) 2;

//		byte d = c-1;
//		byte e = c-(byte) 1;	
//		byte f = (byte) 127 + (byte) 1;
		
		final byte g =2; 
		byte h = g-1;
	}
}

```

注释部分编译报错

一开始一直以为编译器连变量c的数据类型都找不到
最后发现是因为有溢出的风险时在编译时就会报错。。


### 总结

- 对于变量short，byte型的+-都会提成int型进行计算，不损失精度,可能溢出。
- 对于变量short，byte型的+=不会提成int，强转不会溢出，损失精度
- 可以尝试反编译一下看看
- 规则都是有原因的