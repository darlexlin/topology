# 网络结构

## 1 路由

- 在ROS中设置L7协议，按照域名分流的策略路由，

  [^参考1]: [routeros：IP分流/DNS劫持与分流/广告屏蔽/恶意IP屏蔽/HE DDNS_garindu的博客-CSDN博客](https://blog.csdn.net/garindu/article/details/106291387)
  [^参考2]: [ROS通过L7解决dns泥水？ - 软件系统交流 - Chiphell - 分享与交流用户体验](https://www.chiphell.com/thread-2218105-1-1.html)

  示例：

  ```
  /ip firewall layer7-protocol
  add name=gfwlist regexp="google.com|youtube.com|v2fly.org|v2ray.com"
  ```

- 设定黑名单[gfwlist](https://github.com/gfwlist/gfwlist)自动更新

  [^参考]: [gfwlist/gfwlist: The one and only one gfwlist here (github.com)](https://github.com/gfwlist/gfwlist)

  > 此黑名单还需要通过base64进行解密才能使用，待研究

- 黑名单之内的域名通过翻墙网关192.168.0.254

  > /ip firewall mangle
  >
  > add chain=prerouting action=mark-routing new-routing-mark=route2gfw passthrough=yes layer7-protocol=gfwlist  in-interface=bridgeLocal dst-address-type=!local comment="GFW"
  >
  > /ip route
  >
  > add check-gateway=ping distance=1 gateway=192.168.0.254 routing-mark=route2gfw comment="Route2V2ray"

- 黑名单之外的域名通过ROS本机网关192.168.0.1

  > 默认路由

  

## 2 网关

设置两个网关

- ROS本机网关
- 翻墙网关

### 2.1 [ROS本机网关]()

- 略

### 2.2 [翻墙网关]()

创建一个docker，命名为：Docker-Gateway-V2ray

- 基于base镜像制作

- ~~开启IP转发~~

  > 貌似不需要开启转发也可以完成路由功能
  >
  > 开启此项是为了应对多网口设备
  >
  > 然而旁路由不存在此问题

- 安装v2ray，iptables

- 设置好v2ray的参数，iptables按照教程进行设置即可

注：翻墙网关将自己的网关指向ROS，此网关不存在策略，所有经过此网关的流量均翻墙出国。

  > 自建一个v2ray的docker，用于翻墙，网关指向ROS
  > ROS设置IP分流



## 3 DNS

### 3.1 方案一

```
使用ros进行分流，设置黑名单
分流后设置两个Adguard
一个国内，一个国外
```

实现方法

```
adguard1
adguard2
```

### 3.2 方案二

```
使用dnsmasq进行分流，设置白名单或者黑名单
分流后设置两个Adguard
一个国内，一个国外
```

实现方法

```
dnsmasq	+	adguard（国内）
v2ray	+	adguard（国外）
```

### 3.3 方案三

```
使用chinadns同时查询国内外，比较速度
```

实现方法

```
chinadns+	adguard
v2ray	+	adguard
```

## 4 FQ

```
v2ray
```

