---
layout: post
title:  "Zookeeper应用：服务端上下线" 
date:   2018-06-10 15:40:56
categories: jekyll update
tags: [Zookeeper]
---

## 需求

客户端感知服务器的上下线。



## 示意图

![]({{ site.url }}/images/zookeeper上下线机器.png)

### 步骤
服务端：
1、所有机子向Zookeeper注册，注册znode为临时的。

2、有机子下线，连接断开后被Zookeeper自动删除，触发监听事件。

3、有机子上线，触发监听事件。



客户端：
1、连接Zookeeper，获取服务器注册的znode，getchildren()，并注册监听。

2、当Zookeeper触发监听，会rpc远程调用process。

3、process调用getchildren().



服务端代码

```
package hello.zookeeper.schange;  
  
import org.apache.zookeeper.CreateMode;  
import org.apache.zookeeper.WatchedEvent;  
import org.apache.zookeeper.Watcher;  
import org.apache.zookeeper.ZooDefs.Ids;  
import org.apache.zookeeper.ZooKeeper;  
  
public class Server {  
  
    private String connUrl = "zk1:2181,zk2:2181,zk3:2181";  
    private int outTime = 1000;  
    private String parentNode="/servers/";//Zookeeper先建好这个持久节点  
  
    private ZooKeeper zk = null;  
  
    public void getConnection() throws Exception {  
  
        zk = new ZooKeeper(connUrl, outTime, new Watcher() {  
  
            @Override  
            public void process(WatchedEvent event) {  
                System.out.println("Server Watcher：" + event.getType() + "---" + event.getPath());  
            }  
        });  
    }  
  
    public void registerServer(String server) throws Exception {  
        String msg = zk.create(parentNode+server, server.getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);  
        System.out.println("registerServer：" + msg);  
    }  
  
    public static void main(String[] args) throws Exception {  
  
        String name = "server4";  
        Server server = new Server();  
        server.getConnection();  
        server.registerServer(name);  
  
        System.out.println(name + "----start------");  
  
        Thread.sleep(Long.MAX_VALUE);  
    }  
  
}  
```

客户端代码

```
package hello.zookeeper.schange;  
  
import java.util.List;  
  
import org.apache.zookeeper.WatchedEvent;  
import org.apache.zookeeper.Watcher;  
import org.apache.zookeeper.ZooKeeper;  
  
public class Client {  
      
    private String connUrl = "zk1:2181,zk2:2181,zk3:2181";  
    private int outTime = 1000;  
    private String parentNode="/servers";  
  
    private ZooKeeper zk = null;  
  
    public void getConnection() throws Exception {  
  
        zk = new ZooKeeper(connUrl, outTime, new Watcher() {  
  
            @Override  
            public void process(WatchedEvent event) {  
                System.out.println("Client Watcher：" + event.getType() + "---" + event.getPath());  
                try {  
                    getServerList();  
                } catch (Exception e) {  
                    e.printStackTrace();  
                }  
            }  
        });  
    }  
      
      
    public void getServerList() throws Exception{  
          
        List<String> children = zk.getChildren(parentNode, true);  
        if(children!=null&&children.size()>0){  
            System.out.println("服务器列表如下：");  
            for(String child:children){  
                System.out.println(child);  
            }  
        }  
    }  
      
      
    public static void main(String[] args) throws Exception {  
        Client client=new Client();  
        client.getConnection();  
        client.getServerList();  
          
        Thread.sleep(Long.MAX_VALUE);  
    }  
      
      
}  
```

运行结果
开了4台服务端，3台客户端
![]({{ site.url }}/images/注册zookeeper结果.png)




Zookeeper里/servers节点下有4台注册在线的服务器

![]({{ site.url }}/images/注册zookeeper服务器1.png)



客户端运行获取服务端列表

![]({{ site.url }}/images/客户端运行获取服务端列表.png)



服务器下线一台，三个客户端都受到通知

![]({{ site.url }}/images/服务器下线一台.png)





-------------

更多的Java，Angular，Android，大数据，J2EE，Python，数据库，Linux，Java架构师，：

http://www.cnblogs.com/zengmiaogen/p/7083694.html

