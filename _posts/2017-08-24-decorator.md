---
layout: post
title:  "decorator-module"
date:   2014-08-24 15:40:56
categories: jekyll update
---
## 装饰模式
**在不改变原有实现的基础上，实现功能的扩展。并且可以多次叠加扩展。**
**被装饰者始终不变。装饰者在设定了要装饰对象以后也变成了一个被装饰者。**
![][image-1]

实例：咖啡店
咖啡店有多种咖啡，每种有对应的价格。
咖啡可以任意添加调味，比如，牛奶，糖，豆浆…
要计算 加一份牛奶，两份糖的拿铁咖啡的价格。

baseobject.h
{% highlight ruby %}
\#include \<iostream\>
\#include \<string.h\>
using namespace std;

class BaseObject{
public:
BaseObject();
virtual BaseObject();
virtual float cost();
void display();

protected:
float value;
string name;
};
{% endhighlight %}
baseobject.cc
{% highlight ruby %}
\#include "baseobject.h"

BaseObject::BaseObject(){
this-\>value = 0;
}

BaseObject::BaseObject(){

}

float BaseObject::cost(){
return value;
}

void BaseObject::display(){
cout \<\< this-\>name \<\< " : " \<\< this-\>value \<\< endl;
}
{% endhighlight %}
americano.h
{% highlight ruby %}
\#include \<iostream\>
\#include "baseobject.h"
using namespace std;

class Americano : public BaseObject{
public:
Americano();
Americano();
};
{% endhighlight %}
americano.cc
{% highlight ruby %}
\#include "americano.h"
Americano::Americano(){
value = 12.5;
name = "Americano";
display();
}
Americano::Americano(){
cout \<\< "delete Americano" \<\<endl;
}
{% endhighlight %}
flavourobject.h
{% highlight ruby %}
\#include \<iostream\>
\#include "../coffee/baseobject.h"
using namespace std;

class Flavourobject : public BaseObject{
public:
Flavourobject();
Flavourobject();

virtual void setComponent(BaseObject* obj);
virtual float cost();

protected:
BaseObject* obj;
};
{% endhighlight %}
flavourobject.cc
{% highlight ruby%}
\#include "flavourobject.h"

Flavourobject::Flavourobject(){
value = 3;
}

Flavourobject::Flavourobject(){

}

float Flavourobject::cost(){
return this-\>value + this-\>obj-\>cost();
}

void Flavourobject::setComponent(BaseObject* obj){
this-\>obj = obj;
}
{% endhighlight %}
milk.h
{% highlight ruby %}
\#include \<iostream\>
\#include "../coffee/baseobject.h"
\#include "../flavour/flavourobject.h"
using namespace std;

class Milk : public Flavourobject{
public:
Milk();
Milk();
};
{% endhighlight %}
milk.cc
{% highlight ruby%}
\#include "milk.h"

Milk::Milk(){
value = 3;
name = "Milk";
display();
}

Milk::Milk(){

}
{% endhighlight %}
suger.h
{% highlight ruby %}
\#include \<iostream\>
\#include "../coffee/baseobject.h"
\#include "../flavour/flavourobject.h"
using namespace std;

class Suger : public Flavourobject{
public:
Suger();
Suger();
};

{% endhighlight %}
main.cc
{% highlight ruby %}
\#include \<iostream\>
\#include "coffee/baseobject.h"
\#include "coffee/americano.h"
\#include "flavour/milk.h"
\#include "flavour/suger.h"

using namespace std;

int main() {
BaseObject* obj = new Americano();
Flavourobject* flavour = new Milk();
flavour-\>setComponent(obj);
cout \<\< "total:" \<\< flavour-\>cost() \<\< endl;
Flavourobject* flavour1 = new Suger();
flavour1-\>setComponent(dynamic_cast\<BaseObject*\>(flavour));
cout \<\< "total1:" \<\< flavour1-\>cost() \<\< endl;
std::cout \<\< "Hello, World!" \<\< std::endl;
return 0;
}

{% endhighlight %}


[image-1]:	../images/body-bg.jpg