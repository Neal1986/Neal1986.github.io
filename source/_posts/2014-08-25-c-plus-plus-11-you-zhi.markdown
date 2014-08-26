---
layout: post
title: "C++11 右值"
date: 2014-08-25 17:31:49 +0800
comments: true
categories: [C++ 右值]
keywords: c++ 右值
---

   之前学习C++11时对右值的应用理解不是很到位，所幸今天又复习了一遍。

#### 一、左值和右值定义理解

  * 左值通俗定义是非临时对象，可以在多条语句中使用，C++ references定义是：左值可以给存在的object一个别名，例如平时使用的变量

  * 右值就是临时对象，使用完成后即失效， 同样c++ references提到， 右值可用来延续一个临时变量的生命周期

  左值用&表示， 右值用&&。

程序1：
    void func(int& a)
    {
        std::cout<<"this is a lvalue"
    }

    void func(int&& a)
    {
        std::cout<<"this is a rvalue"
    }

    int main()
    {
        int value=3;
        func(value);
        func(100);
    }

#### 二、为什么要引进右值

##### 1. 消除对象不必要的拷贝，节省运算存储资源, 提高效率

   这个很好解释，如上面的代码，如果不用右值，那么在调用func(100)的时候存在一次重新构造变量a的过程。
另外在定义class的时候，添加实现**转移构造函数**和**转移赋值函数**，可以避免对临时变量的构造和拷贝过程. 下面是示例，编译记得加C++ 11的选项。

程序2:
    #include<stdio.h>
    #include<string>
    #include<iostream>

    class R_value
    {
    private:
        int *i;
        R_value(R_value& a): i(a.i){std::cout<<a.i<<" copy construct"<<std::endl;}
        R_value& operator=(R_value& a) {std::cout<<(i=a.i)<<"11 equal construct"<<std::endl;}

    public:
        R_value(int* a): i(a){std::cout<<i<<"construct"<<std::endl;}
        ~R_value(){std::cout<<i<<"deconstruct"<<std::endl;}

        R_value(const R_value& a):i(a.i){std::cout<<a.i<<"const copy construct"<<std::endl;}
        R_value& operator=(const R_value& a) {std::cout<<(i=a.i)<<"const equal construct"<<std::endl;}

        R_value(R_value&& a):i(a.i){std::cout<<a.i<<"R copy construct"<<std::endl;}
        R_value& operator=(R_value&& a){std::cout<<(i=a.i)<<"R copy construct"<<std::endl;}

        int& operator*() const {return *i;}
    };

    R_value func()
    {
        return R_value(new int(4));
    }

    int main()
    {
        //R_value construct(new int(3));
        //R_value Copyconstruct(construct);
        const R_value&& test = func();
        R_value a(test);
        R_value b(std::move(a));
        //system("pause");
        return 0;
    }

##### 2. 精确传递(perfect Forwarding)

  精确传递就是将一组参数原封不动的传递给另外一组函数。

在C++中除参数值外，还有以下两组属性：
  左值和右值，const和non const.

精确传递就是在参数传递过程中，所有这些属性和参数值都不能改变。在泛型函数中，这样的需求非常普遍.
如以下forward_value函数将一个参数传递给另一个函数，比如程序1中的func()，

程序3：
    template<typname T>
    void forward_value(T&& value)
    {
       func(value);
    }

    int valueA = 2;
    const int& valueB = 3;
    forward_value(valueA); //int&
    forward_value(valueB); //const int&
    forward_value(4); //int&&

  这样一来就不用写两个模板(const, non const)
