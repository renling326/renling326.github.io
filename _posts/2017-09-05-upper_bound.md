---
layout: post
title:  "upper_bound()与lower_bound()使用方法"
date:   2017-09-04 15:40:56
categories: jekyll update
---
upper_bound()与lower_bound()使用方法

￼
转载
▼

标签： 杂谈

分类： STL

{% highlight ruby %}
# include <iostream>
# include <algorithm> //必须包含的头文件
using namespace std;
int main(){
  int point[10]() = {1,3,7,7,9};
  int tmp = upper\_bound(point, point + 5, 7) - point; //按从小到大，7最多能插入数组point的哪个位置 
  printf("%d\n",tmp);
  tmp = lower\_bound(point, point + 5, 7) - point; //按从小到大，7最少能插入数组point的哪个位置
  printf("%d\n",tmp);
  return 0;
}
{% endhighlight %}

output:

4
2

