---
layout: post
title:  "算法模式" 
date:   2017-08-28 15:40:56
categories: jekyll update
---

__模板算法模式：__
在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以在不改变算法结构的基础上，重新定义算法中的某些步骤

类图：

![]({{ site.url }}/images/模板算法模式.png)

一个简单的例子：
冲泡一杯咖啡和冲泡一杯茶的步骤很相似

1. 烧水
2. 冲泡
3. 倒入杯子
4. 加入调料

因此可以将准备的过程在封装在基类中，具体的实现方法，可以由子类实现。

咖啡因类饮料
{% highlight ruby %}
#include <iostream>
#include <string.h>
using namespace std;

class CoffeineBeverage{
public:
    CoffeineBeverage();
    virtual ~CoffeineBeverage();
    void prepareRecipe();
    void boilWater();
    virtual void brew() = 0;
    void pourInCup();
    virtual void addCondiments() = 0;
};

#include "coffeinebeverage.h"

CoffeineBeverage::CoffeineBeverage(){

}

CoffeineBeverage::~CoffeineBeverage(){

}

void CoffeineBeverage::prepareRecipe(){
    boilWater();
    brew();
    pourInCup();
    addCondiments();
}

void CoffeineBeverage::boilWater(){
    cout << "boilWater" << endl;
}

void CoffeineBeverage::pourInCup(){
    cout << "pourInCup" << endl;
}
{% endhighlight %}

咖啡
{% highlight ruby %}
#include <iostream>
#include <string.h>
#include "coffeinebeverage.h"
using namespace std;

class Coffee : public CoffeineBeverage{
public:
    Coffee();
    virtual ~Coffee();
    virtual void brew;
    virtual void addCondiments();
};
#include "coffee.h"

Coffee::Coffee(){

}

Coffee::~Coffee(){

}

void Coffee::brew(){
    cout << "brewCoffeeGrinds" << endl;
}

void Coffee::addCondiments(){
    cout << "addSugarAndMilk" << endl;
}
{% endhighlight %}

茶
{% highlight ruby %}
#include <iostream>
#include <string.h>
#include "coffeinebeverage.h"
using namespace std;

class Tea : public CoffeineBeverage{
public:
    Tea();
    virtual ~Tea();
    virtual void brew();
    virtual void addCondiments();
};

#include "tea.h"

Tea::Tea(){

}

Tea::~Tea(){

}

void Tea::brew(){
    cout << "brewTea" << endl;
}

void Tea::addCondiments(){
    cout << "addLemon" << endl;
}

{% endhighlight %}

__钩子是一种方法__
它在抽象类中不做事，或者只做默认的事情，子类可以选择要不要覆盖他，通过钩子子类可以对算法的某些流程进行干预

{% highlight ruby %}
void CoffeineBeverage::prepareRecipe(){
    boilWater();
    brew();
    pourInCup();
    if(isCondiments())
        addCondiments();
}
bool CoffeineBeverage::isCondiments(){
    return true;
}
{% endhighlight %}

**好莱坞原则**
决策权放在高层模块中，以便决定如何以及何时调底层模块

**与策略模式的区别**
策略模式和模板方法模式都封装算法，一个用组合，一个用继承。策略模式用委托的方式实现。

**与工厂模式**
工厂模式是模板方法模式的特殊版本