---
layout: post
title:  "使用Python创建简单的HTTP服务(基于SimpleHTTPServer) 和 FTP服务(基于pyftpdlib)"
date:   2017-09-07 15:40:56
categories: jekyll update
---
不管工作中还是其他场合，经常会有文件分享的需求，比如自己下了一个4GB的游戏，同事下了一个800MB的软件，其他人如果也需要这些文件，显然直接分享是最快捷、最方便、最环保的方式了，如果再重新下，既浪费时间，又浪费电。Windows自带了文件夹共享的功能，不过这玩意儿，至今我还没仔细的去折腾过，也没搞明白原理，我一直使用FTP来分享文件，如果有需要的话再弄个HTTP服务也足够应付了，我的目的仅仅只是文件分享，简单、易用是我主要关注的点。

**反复可耻**

这种需求必然是非常普遍的，解决方法也是多种多样。比如飞鸽传书、QQ，或者各种局域网通信软件以及网盘都可以，不过这些都需要点对点的操作，也就是说对方需要什么文件，你就要把该文件发送过去，多一个人就需要多发一次，不能重用，对于程序员来说，不能接受，反复可耻！我觉得，文件服务器更符合这种需求，目前已经有很多成熟的软件可以方便的实现这种需求，比如，FTP可以选择FileZilla，HTTP可以选择Apache、Nginx。当然，成熟意味着软件更复杂，更大的学习成本、更多磁盘占用，这要看你的需求的复杂程度。

**折腾至死**

好了，我需要说明，我是个喜欢折腾的人，将一件看起来复杂、学习成本高的事情，折腾到简单、易用是我的终极爱好，这篇文章正是这种折腾后的产物，希望直接达到目的的，成熟的软件是你最好的归宿。喜欢折腾，换种程序员喜欢的说法是，我热衷于把复杂的实现，隐藏在良好命名的接口下。再换种通俗点的说法，汽车内部构造精良、结构复杂，而你只需要通过操作方向盘、刹车以及油门，就可以很好的操控它，这都归功于这些接口的友好实现。

同样的事情，只需要一遍就好。在平时写代码，也是这样。如果我需要别人提供的一个接口，那我希望这个接口使用起来尽量简单，我只需要知道调用什么接口、传什么参数就行。我自己写代码，一定严格按照这种思路，我不认为使用者需要关注我是如何实现的，那是我的问题，使用者需要关注的是这个接口是干什么的、需要什么参数、可能返回的结果以及可能抛出的错误，分工明确，减少不必要的沟通。

我在前面一遍文章中有提到Python--导出指定文件夹中as文件的完全限定类名，好处我就不多说了，一个字，性感！很巧的是，我在折腾这个文件分享的功能时，遇到了他，借着这个机会，又复习了一遍HTTP和FTP方面的内容。

**Python版HTTP服务器**

闲话少说，首先确保装了Python，我装的是2.x版本，对了，我的操作系统是WIN7，其实对于Python来说，什么操作系统并不重要。Python内置了一个简单的HTTP服务器，只需要在命令行下面敲一行命令，一个HTTP服务器就起来了：

{% highlight ruby %}
python -m SimpleHTTPServer 80
{% endhighlight %}

后面的80端口是可选的，不填会采用缺省端口8000。注意，这会将当前所在的文件夹设置为默认的Web目录，试着在浏览器敲入本机地址：

{% highlight ruby %}
http://localhost:80
{% endhighlight %}

如果当前文件夹有index.html文件，会默认显示该文件，否则，会以文件列表的形式显示目录下所有文件。这样已经实现了最基本的文件分享的目的，你可以做成一个脚本，再建立一个快捷方式，就可以很方便的启动文件分享了。如果有更多需求，完全可以根据自己需要定制，具体的请参见官方文档SimpleHTTPServer，或者直接看源码。我拷贝一段，方便参考：

{% highlight ruby %}
import SimpleHTTPServer
import SocketServer

PORT = 8000

Handler = SimpleHTTPServer.SimpleHTTPRequestHandler

httpd = SocketServer.TCPServer(("", PORT), Handler)

print "serving at port", PORT
httpd.serve_forever()
{% endhighlight %}

**Python版FTP服务器**

看到这里，默认你已经安装了Python，不过你还需要安装另外一个好用的工具。你知道，当需要找Chrome插件的时候，会去Google的WebStore；当需要找Firefox应用的时候，会去Mozilla的Add-ons；当你需要找Python组件的时候，你需要pip:A tool for installing and managing Python packages，安装方法就不介绍了。

Python没有内置一个直接可以用的FTP服务器，所以需要第三方组件的支持，我找到的这个组件叫pyftpdlib，首先安装：

{% highlight ruby %}
pip install pyftpdlib
{% endhighlight %}

安装完后，和HTTP服器类似，执行以下命令就可以启动一个FTP服务器了：

{% highlight ruby %}
python -m pyftpdlib -p 21
{% endhighlight %}

后面的21端口依然是可选的，不填会随机一个，被占用的端口将跳过。在浏览器敲入本机地址：

{% highlight ruby %}
ftp://localhost:21
{% endhighlight %}

这时候，是匿名访问，也就是用户名是anonymous，密码为空，如果想要控制访问权限，你需要自己定制服务器，具体的可以参看pyftpdlib Tutorial，我这里拷贝过来一段作为介绍：

{% highlight ruby %}
from pyftpdlib.authorizers import DummyAuthorizer
from pyftpdlib.handlers import FTPHandler
from pyftpdlib.servers import FTPServer

def main():
    # Instantiate a dummy authorizer for managing 'virtual' users
    authorizer = DummyAuthorizer()

    # Define a new user having full r/w permissions and a read-only
    # anonymous user
    authorizer.add_user('user', '12345', '.', perm='elradfmwM')
    authorizer.add_anonymous(os.getcwd())

    # Instantiate FTP handler class
    handler = FTPHandler
    handler.authorizer = authorizer

    # Define a customized banner (string returned when client connects)
    handler.banner = "pyftpdlib based ftpd ready."

    # Specify a masquerade address and the range of ports to use for
    # passive connections.  Decomment in case you're behind a NAT.
    #handler.masquerade_address = '151.25.42.11'
    #handler.passive_ports = range(60000, 65535)

    # Instantiate FTP server class and listen on 0.0.0.0:2121
    address = ('', 2121)
    server = FTPServer(address, handler)

    # set a limit for connections
    server.max_cons = 256
    server.max_cons_per_ip = 5

    # start ftp server
    server.serve_forever()

if __name__ == '__main__':
    main()

{% endhighlight %}

只看代码应该基本知道该怎么用了，add_user显然是添加用户，2121是指定端口，当然也可以随机，还有最大连接数max_cons，每个ip最大连接限制，更多的接口建议直接看docstrings。

**后记**

Python第三方组件就是个大宝库，基本上我自己遇到的大部分问题都可以在这里面找到解决文案。同时，建议喜欢折腾的程序员，甚至没有程序背景的IT人员，都尝试学习一下这门语言，这对解决问题的能力以及思维的锻炼都有莫大裨益。

文章转载自[http://www.cnblogs.com/yili16438/p/d3209323913c6d53e6060fcd8d27e4c0.html][1]

[1]:	http://www.cnblogs.com/yili16438/p/d3209323913c6d53e6060fcd8d27e4c0.html
