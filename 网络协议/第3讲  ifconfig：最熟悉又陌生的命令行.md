# 如何查看ip地址

| Windows    | Linux      |
| ---------- | ---------- |
| `ipconfig` | `ifconfig` |
|            | `ip addr`  |
|            |            |

## ifconfig和ip addr的区别

ifconfig :  net-tools

ip addr:   iproute2

# IP地址类别

**IP地址是一个网卡在网络世界的通讯地址，相当于我们现实世界的门牌号码。**

## Ipv4 和 ipv6

IPv4：每个部分8个bit，所以IP地址总共是32位 `10.100.122.2`

由于目前计算机数量的增加，ipv4地址明显不够，因此出现了ipv6

IPV6：每个部分16个bit，总共有128位

## IP地址类别分布

ip地址被分为5类

![img](https://static001.geekbang.org/resource/image/0b/9e/0b32d6e35ff0bbc5d46cfb87f6669d9e.jpg)

ABC类地址主要包含：**网络号和主机号**

下表主要展示A、B、C地址的数量

![img](https://tva1.sinaimg.cn/large/007S8ZIlly1gir0xlfkikj31080aatck.jpg)

产生问题：

- C类地址能包含的最大主机数量实在太少了，只有254个
- B类地址能包含的最大主机数量又太多了

因此有个一种解决方案：无类型域间选路（CIDR Classless Inter-Domain Routing）

## 无类型域间选路（CIDR）

- 打破了原来设计的几类地址的做法，将32位的IP地址一分为二，前面是**网络号**，后面是**主机号**
- `10.100.122.2/24`:斜杠后面有个数字24，这种就是基于CIDR地址表示形式，前24位是网络号，后8位是主机号
- 伴随着CIDR存在：广播地址 和 子网掩码

| ip                | 广播地址         | 子网掩码        |
| ----------------- | ---------------- | --------------- |
| `10.100.122.2/24` | `10.100.122.255` | `255.255.255.0` |

**将子网掩码和IP地址按位计算AND，就可得到网络号。**

## 公有ip和私有ip

![img](https://static001.geekbang.org/resource/image/90/93/901778433f2d6e27b916e9e53c232d93.jpg)

私有IP：这些地址允许组织内部的IT人员自己管理、自己分配，而且可以重复。

公有IP：地址有个组织统一分配，你需要去买。

## 出口地址和广播地址

整个网络里面的第一个地址192.168.0.1，往往就是私有网络的出口地址。

192.168.0.255就是广播地址，一旦发送这个地址，整个192.168.0网络里面的所有机器都能收到。

## 组播地址

使用这一类地址，属于某个组的机器都能收到。这有点类似在公司里面大家都加入了一个邮件组，发送邮件，加入这个组的都能收到。

## SCOPE

```shell
root@test:~# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether fa:16:3e:c7:79:75 brd ff:ff:ff:ff:ff:ff
    inet 10.100.122.2/24 brd 10.100.122.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fec7:7975/64 scope link 
       valid_lft forever preferred_lft forever
```

scope:

- 对于eth0来讲，是global，说明这张网卡是可以对外的，可以接收来自各个地方的包。
- 对于lo来讲，是host，说明这张网卡仅仅可以供本机相互通信

lo全称是**loopback**，又称**环回接口**，往往会被分配到127.0.0.1这个地址。这个地址用于本机通信，经过内核处理后直接返回，不会在任何网络中出现。

# MAC地址

ip地址的上一行：`link/ether fa:16:3e:c7:79:75 brd ff:ff:ff:ff:ff:ff`

是一个网卡的物理地址，用十六进制，6个byte表示

MAC地址具有全局唯一性，不会有两个网卡有相同的MAC地址

## 为什么互联网通信不用MAC地址

**一个网络包要从一个地方传到另一个地方，除了要有确定的地址，还需要有定位功能**。而有门牌号码属性的IP地址，才是有远程定位功能的。

- MAC地址更像是身份证，是一个唯一的标识。
- 唯一性设计是为了组网的时候，不同的网卡放在一个网络里面的时候，可以不用担心冲突
- MAC地址有一定的定位功能，但范围非常有限：可局限于一个子网内。例如，从192.168.0.2/24访问192.168.0.3/24是可以用MAC地址的。一旦跨子网，即从192.168.0.2/24到192.168.1.2/24，MAC地址就不行了，需要IP地址起作用了。

# 网络设备的状态标识

**`<BROADCAST,MULTICAST,UP,LOWER_UP>  mtu 1500`表示什么？**

net_device flags，网络设备的状态标识。

UP：表示网卡处于启动状态

BROADCAST：表示这个网卡有广播地址，可以发送广播包

MULTICAST：表示网卡可以发送多播包

LOWER_UP：表示L1是启动的，也即网线插着呢

MTU1500：表示最大传输单元MTU为1500，这是以太网的默认值。

MAC层有MAC的头，以太网规定连MAC头带正文合起来，**不允许超过1500个字节**。正文里面有IP的头、TCP的头、HTTP的头。如果放不下，就需要分片来传输。



**qdisc pfifo_fast表示什么？**

**queueing discipline**，排队规则。内核如果需要通过某个网络接口发送数据包，它都需要按照为这个接口配置的qdisc（排队规则）把数据包加入队列。

- 最简单的qdisc是pfifo，它不对进入的数据包做任何的处理，数据包采用先入先出的方式通过队列。

- pfifo_fast稍微复杂一些，按照服务类型（**Type of Service，TOS**）将数据包分配到三个波段（band）里面的（TOS是IP头里面的一个字段），代表了当前包的优先级。在每个波段里面，使用先进先出规则。（如果band 0里面有数据包，系统就不会处理band 1里面的数据包，band 1和band 2之间也是一样。）

