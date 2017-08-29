---
layout: post
title:  "单例模式"
date:   2017-08-27 15:40:56
categories: jekyll update
---
保证一个类只有一个实例，并提供一个访问它的全局访问点。首先，需要保证一个类只有一个实例；在类中，要构造一个实例，就必须调用类的构造函数，如此，为了防止在外部调用类的构造函数而构造实例，需要将构造函数的访问权限标记为protected或private；最后，需要提供要给全局访问点，就需要在类中定义一个static函数，返回在类内部唯一构造的实例。类图:

![]({{ site.url }}/images/单例模式.png)

单例模式有两种实现方式：懒汉模式和饿汉模式。
   懒汉模式是以时间换空间。
   懒汉模式是以空间换时间。

懒汉模式：

{% highlight ruby %}
# include <iostream>
# include <string.h>
using namespace std;

class Singleton{
public:
static Singleton * getInstance();

private:
Singleton();
Singleton();
static Singleton * singleton;
static void Destory();
static pthread_mutex_t mutex;
};

# include "singleton.h"

Singleton * Singleton::singleton = NULL;
pthread_mutex_t Singleton::mutex;
Singleton::Singleton()
{

}
Singleton::Singleton(){

}

void Singleton::Destory(){
if(singleton != NULL){
delete singleton;
singleton = NULL;
}
}
Singleton * Singleton::getInstance(){
if(singleton == NULL){
pthread_mutex_lock(&mutex);
if(singleton == NULL){
singleton = new Singleton();
}
pthread_mutex_unlock(&mutex);
}
return singleton;
}

{% endhighlight %}

饿汉模式：

{% highlight ruby %}
# include <iostream>
# include <string.h>
using namespace std;

class Singleton{
public:
static Singleton * getInstance();

private:
Singleton();
Singleton();
static Singleton * m_singleton;
};

# include "singleton.h"

Singleton * Singleton::m_singleton = new Singleton();
Singleton::Singleton()
{

}
Singleton::Singleton(){

}

Singleton * Singleton::getInstance(){
return const_cast <Singleton *>m_singleton;
}


{% endhighlight %}

关于饿汉模式的实例销毁，有一种新的实现方法

{% highlight ruby %}
# include <iostream>
# include <string.h>
using namespace std;

class Singleton{
public:
static Singleton * getInstance();

private:
Singleton();
Singleton();
static Singleton * m_singleton;
};

# include "singleton.h"

Singleton * Singleton::m_singleton = new Singleton();
Singleton::Singleton()
{

}
Singleton::Singleton(){

}

Singleton * Singleton::getInstance(){
static Singleton m_singleton;_
return const_cast <Singleton *>m_singleton;
}

{% endhighlight %}

__总结：懒汉模式适用于一般需要场景，但是当大数据模式下，需要频繁获取实例请求的时候，加锁可能会成为性能瓶颈。因此在需要大量频繁调用的情况下，可以使用饿汉模式__
**单例模式使用场景**：

1. 配合工厂模式使用
2. 日志对象
3. 读写对象
