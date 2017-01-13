---
layout: post
title: 【捌·折腾】karabiner鼠标映射
---

### 写在前边的废话

读了半天Apache Mina的文档，英文有些蛋疼，想到之前mac有个三指点击翻译的功能，用鼠标的话不太方便，一直在用karabiner做方向键映射，想把翻译这个操作映射到鼠标中键上

### 配置
- 查词映射鼠标中键

```xml

	<autogen> 
		__KeyToKey__
		PointingButton::MIDDLE,
		KeyCode::D,ModifierFlag::CONTROL_L,ModifierFlag::COMMAND_L
	</autogen>
```

- 顺便发现了取缔ScrollReverser的配置，少一个是一个

```xml

	<autogen>
    		__FlipScrollWheel__
    		Option::FLIPSCROLLWHEEL_VERTICAL
  	</autogen>
```


### 小结
- 网上貌似没有改鼠标映射的，摸索了一段时间发现鼠标用的是PointingButton::而不是KeyCode::
- Safari下，该快捷键点击链接会弹出预览窗口，还挺好用
