---
layout: post
title: 【拾壹·学习】Callback&Observer
---

### 写在前边的废话

新项目没人带，硬着头皮直接啃起了项目，发现了一些有趣的东西，也算是终于搞懂了观察者模式和java回调机制。

### 概念理解

- 观察者模式:

  __我觉得叫发布--订阅模式理解起来容易多了，因为需要被观察者(发布者)调用方法才能推送，而被观察者听起来没有主动发送的职能，所以还是叫发布-订阅模式比较好__
  - 定义发布者与订阅者两个接口
  - 发布者实体类中维护订阅者接口的List

- 回调机制
  __事实上回调与观察者是没有关系的，但是可以在观察者模式中加入回调机制__
  - 回调机制:在java中，A类中方法f(this)中调用 B类中方法m(),m()中通过入参"this"调用A类中方法
  - 用指针更容易理解:如果你把函数的指针（地址）作为参数传递给另一个函数，当这个指针被用来调用其所指向的函数时，我们就说这是回调函数

  
### 实例

网上有些回调的讲解有误，比如下面这种并不是回调，只是一种解耦的机制

```
public class TestInterface {
    public static void main (String[] args){
        A a = new A(new B() {
            @Override
            public void update() {
            }
        });
        a.callB();
    }
}
class A{
    B b;
    A(B b){
        this.b = b;
    }
    public void callB(){
        b.update();;
    }
}
interface B{
    public void update();
}
```

- 在A中调用B中方法，通过接口解耦而已，并没有涉及回调概念

#### 回调

```
public class TestCallBack {
    public static void main(String[] args){
//      A1 a = new A1(new B1impl()); //单独写出类的实现更容易理解一些

        A1 a = new A1(new B1() {
            @Override
            public void update(A1 a) {
                a.addV();
                System.out.println(a.getV());
            }
        });
        a.letBModifyV();
    }

}
class A1 {
    private int v = 0;
    B1 b;
    A1(B1 b){
        this.b = b;
    }
    public void letBModifyV(){
        b.update(this);
    }
    public void addV(){
        v ++;
    }
    public int getV(){
        return  v;
    }
}
interface B1{
    public void update(A1 a);
}
//class B1impl implements B1{
//    @Override
//    public void update(A1 a) {
//        a.addV();
//        System.out.println(a.getV());
//    }
//}
```

上面代码概括来说是，A让B修改A的V，所以传入了this

#### 观察者模式

```
public class TestObserver {
    public static void main(String[] args){
        A2 a = new A2();
        //理应写类实现Bi，这样remove才有意义，不过不影响理解
        a.add(new Bi() {
            @Override
            public void update() {
                System.out.println("短信订阅");
            }
        });
        a.add(new Bi() {
            @Override
            public void update() {
                System.out.println("微信订阅");
            }
        });
        a.notice();
    }
}
interface Ai{
    public void add(Bi b);
    public void remove(Bi b);
    public void notice();
}
interface Bi{
    public void update();
}
class A2 implements Ai{
    List<Bi> bs = new ArrayList<>();

    @Override
    public void add(Bi b) {
        bs.add(b);
    }

    @Override
    public void remove(Bi b) {
        bs.remove(b);
    }

    @Override
    public void notice() {
        for(Bi b:bs){
            b.update();//如果此处传入this对象，调用A中方法才是回调。所以回调与观察者模式本身没有任何关系
        }
    }
}
```

先在发布者中注册订阅者，然后发布者__主动__发布，订阅者们都收到了消息

### 小结

弄懂一个点其实挺难，误导有些多，不过搞定了一个基本知识点之后很多问题迎刃而解。有时候读得懂项目跟读不懂项目只差了一个基础知识。
