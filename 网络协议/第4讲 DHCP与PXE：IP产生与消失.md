# 如何配置IP地址？

配置IP地址有以下两种方式：

- `ifconfig`
- `ip addr`

**使用`net-tools`:**

```shell
$ sudo ifconfig eth1 10.0.0.1/24
$ sudo ifconfig eth1 up
```

**使用`iproute2`：**

```shell
$ sudo ip addr add 10.0.0.1/24 dev eth1
$ sudo ip link set up eth1
```

# 如何发出去一个包？

假设你自己的IP地址（源IP地址）：`16.158.23.6`

目标机器的IP地址：`192.168.1.6`

1. Linux首先会判断，目标IP和源IP是否在同一网段，如果在同一网段发送ARP请求，获取MAC地址
2. 如果是跨网段调用，**不会直接将包发送到网络上，而是获取网关的MAC地址，企图将包发送到网关。**

