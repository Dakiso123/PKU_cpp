# C++面向对象程序设计(六)——多态

本文是中国大学MOOC，北京大学[程序设计与算法C++面向对象程序设计](https://www.icourse163.org/learn/PKU-1002029030#/learn/announce)第六周笔记。本课程学习的[github仓库](https://github.com/mrcangye/PKU_Cplus_2020Spring)欢迎Fork

[toc]

## 虚函数和多态

### 虚函数

类的定义中，前面有`virtual`关键字的成员函数就是虚函数

```cpp
class base{
    virtual int get();
};
int base::get(){}
```

`virtualenv`关键字只用在类定义里的函数声明中，写函数体时不用。

**构造函数和静态成员函数不能是虚函数**，派生类和基类同名同参数表的函数，不加`virtualenv`也自动成为虚函数



虚函数与普通函数的本质区别是，虚函数可以参加多态，普通函数不可以

### 多态

#### 作用

增强程序的可扩充性

#### 派生类的指针可以赋给基类指针

通过基类指针调用基类和派生类中的同名虚函数时：
​	如果该指针指向一个基类对象，那么被调用是基类的虚函数

​	如果指向一个派生类对象，那么被调用的是派生类的虚函数
i.e.,
```cpp
class Base{
    public:
    virtual void SomeVirtualFunction(){};
};
class Derived::public Base{
    public:
    virtual void SomeVirtualFunction(){};
};
int main(){
	Derived derived;
	Base* p = &derive;
	p->SomeVirtualFunction(); //调用哪个虚函数取决于p指向哪种类型的对象(所以执行子类里的函数）
 	return 0;
}
```

#### 派生类的对象可以赋给基类引用

通过基类引用调用基类和派生类中同名虚函数时：
​	如果该引用引用的是一个基类对象，那么被调用是基类的虚函数

​	如果引用的是一个派生类对象，那么被调用的是派生类的虚函数
i.e.,
```cpp
class Base{
    public:
    virtual void SomeVirtualFunction(){};
};
class Derived::public Base{
    public:
    virtual void SomeVirtualFunction(){};
};
int main(){
	Derived derived;
	Base& r = derive;
	r.SomeVirtualFunction(); //调用哪个虚函数取决于r引用哪种类型的对象(所以执行子类里的函数）
 	return 0;
}
```

## 多态的实现原理

每一个有虚函数的类（或有虚函数的类的派生类）都有一个虚函数表，该类的任何对象中都放着虚函数表的指针。虚函数表中列出了该类的虚函数地址。多出来的字节就是用来放虚函数表的地址的

## 虚析构函数

通过基类的指针删除派生类对象时，通常情况下只调用基类的析构函数。但是删除一个派生类对象时，应该先调用派生类的析构函数，然后调用基类的析构函数。这样会冲突

所以我们要把基类的析构函数声明为`virtual`

基类的析构函数是虚函数，派生类的析构函数可以不进行声明`virtual	`

通过基类的指针删除派生类对象时，首先调用派生类的析构函数，然后调用基类的析构函数。

一般来说，一个类如果定义了虚函数，那么析构函数也应该定义成虚函数。或者一个类打算作为基类使用，也应该将析构函数定义成虚函数

但是不允许虚函数作为构造函数

## 纯虚函数和抽象类

### 纯虚函数

没有函数体的虚函数

```cpp
class A{
private:
	int a;
public:
	virtual void Print() = 0;	//纯虚函数
	void fun(){
	cout<<"fun";
	}
}
```

### 抽象类

包含纯虚函数的类叫抽象类，抽象类只能作为基类来派生新类使用，不能创建抽象类的对象

抽象类的指针和引用可以指向由抽象类派生出来的类的对象

```cpp
A a;	//错误，Ａ是抽象类，不能创建对象
A *pa;	//可以，可以定义抽象类的指针和引用
pa = new A;	//错误，A是抽象类，不能创建对象
```

抽象类的成员函数内可以调用纯虚函数，但是在构造函数或析构函数内部不能调用纯虚函数　

如果一个类从抽象类派生而来，那么当且仅当它实现了基类中的所有纯虚函数，它才能成为非抽象类
