---
layout: post
title: 【小坑】Fastjson小坑记录
---

### 写在前边的废话

昨天早上包终于打出来了，连通几宵之后迎来了一个半月以来第一次6：30下班，然后就去吃了个又贵又难吃的烧烤庆祝了一下

fastjson 不陌生了，在上个月的时候遇到了个小坑，今天重构代码的时候又想起他来了，随手记录一下

### 常用方法

- JSON.toJSONString(Object obj);
- JSON.parseObject(String str,Class);

### 基本理解

还没有看过源码（惭愧），不过看这些小坑可以大体总结

- fastjson 是通过getter setter 来转化字符串与对象的
- fastjson 本身是用map存储的

### 字符串转json对象

- get方法命名规范与eclipse自动提示相同，boolean对象需使用isX()不可用get，封装类型Boolean没有这个限制 __//test1__
- 字符串中缺失和多余字段，可以正常转化 __//test2__
- json对象中存在Map，且Map中使用其他对象，使用parseObject转化时会将Map中的对象转为jsonObject，需要遍历中进一步强转才能得到 __//见test3__

### 代码
__TestJson.java__

```java
package json;
import java.util.HashMap;
import java.util.Map;
import java.util.Map.Entry;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;

public class TestJson {
	public static void main(String[] args) {
		//test1
		T t = new T();
		t.setV1(true);
		t.setV2(true);
		t.setV3(true);
		t.setIsV4(true);
		String jsonStr =  JSON.toJSONString(t);
		System.out.println(jsonStr);
		T t1 = JSON.parseObject(jsonStr,T.class);
		System.out.println("v1="+t1.isV1()+"isV2="+t1.getIsV2()+"v3="+t1.getV3()+"v4="+t1.getIsV4());
		
		//test2
		TT tt1 = JSON.parseObject("{\"v1\":1}",TT.class);
		TT tt2 = JSON.parseObject("{\"v1\":1,\"v2\":2,\"v3\":3}",TT.class);
		System.out.println("tt1="+JSON.toJSONString(tt1)+"tt2="+JSON.toJSONString(tt2));
		
		//test3
		Map<Integer, T> map = new HashMap<>();
		map.put(1, new T());
		TTT ttt = new TTT();
		ttt.setMap(map);
		TTT ttt1 = JSON.parseObject(JSON.toJSONString(ttt), TTT.class);
		try {
			for(Entry<Integer, T> entry:ttt1.getMap().entrySet()){
				T error = entry.getValue();
			}
		} catch (Exception e) {
			System.out.println(e);
		}
		for(Entry<?,?> jsonEntry : ttt1.getMap().entrySet()){
			JSONObject jsonObject = (JSONObject)jsonEntry.getValue();
			int key = Integer.parseInt((String)jsonEntry.getKey());
			T tmp = JSON.parseObject(jsonObject.toJSONString(),T.class);
			System.out.println("succ");
		}
		
	}
	
	
}


```

__T.java__

```java
package json;

import java.util.List;

public class T {
	private boolean v1;
	private boolean isV2;
	private Boolean v3;
	private Boolean isV4;
	private List<TT> tmp;
	public List<TT> getTmp() {
		return tmp;
	}
	public void setTmp(List<TT> tmp) {
		this.tmp = tmp;
	}
	public boolean isV1() {
		return v1;
	}
//	public boolean isV2() {
//		return isV2;
//	}
	public boolean getIsV2() {
		return isV2;
	}
	
	public Boolean getV3() {
		return v3;
	}
//	public Boolean isV3() {
//		return v3;
//	}
	public Boolean getIsV4() {
		return isV4;
	}
	
	public void setV1(boolean v1) {
		this.v1 = v1;
	}
	public void setV2(boolean isV2) {
		this.isV2 = isV2;
	}
	public void setV3(Boolean v3) {
		this.v3 = v3;
	}
	public void setIsV4(Boolean isV4) {
		this.isV4 = isV4;
	}
}


```

__TT.java__

```java
package json;

public class TT{
	private int v1;
	private int v2;
	public void setV1(int v1) {
		this.v1 = v1;
	}
	public void setV2(int v2) {
		this.v2 = v2;
	}
	public int getV1() {
		return v1;
	}
	public int getV2() {
		return v2;
	}
}


```

__TTT.java__

```java
package json;

import java.util.Map;

public class TTT{
	private Map<Integer,T> map;
	public Map<Integer, T> getMap() {
		return map;
	}
	public void setMap(Map<Integer, T> map) {
		this.map = map;
	}
}

```

### Console

```
{"isV2":true,"isV4":true,"v1":true,"v3":true}
v1=trueisV2=falsev3=truev4=true
tt1={"v1":1,"v2":0}tt2={"v1":1,"v2":2}
java.lang.ClassCastException: com.alibaba.fastjson.JSONObject cannot be cast to json.T
succ
```

无需多言