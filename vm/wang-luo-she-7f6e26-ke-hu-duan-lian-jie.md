# 虚拟机网络设置 & 远程连接工具


## 1. VM 网络模式

### 1.1 网络模式

VM 虚拟机提供三种网络方式， 桥接模式，仅主机模式，NAT 模式。

* 桥接模式: 虚拟机相当于一台真的物理机一样，会占用网络中的一个ip地址， 能联通互联网
* NAt模式: 虚拟机不能连接互联网，但是能和本机通信，使用网卡VMnet8
* 仅主机模式: 只能和主机通信，使用网卡VMNet1 , 不常用

### 1.2 虚拟网卡

VM 安装成功之后，电脑上会新增两块虚拟网卡 VMnet1 和 VMnet8 ,分别用于仅主机模式和NAT模式, 用于本机和虚拟机之间的通信。通过 **控制面板-&gt;网络和Internet-&gt;网络连接** 可以直观地看到. 如果没有的话, 可以通过1.3 中的 **还原默认配置** 按钮回复,重新生成两块儿虚拟网卡.

![](/assets/vm_network_2017-05-22_182625.png)

### 1.3 查看虚拟网卡ip

在VM 中, 通过 **编辑-&gt; 虚拟网路编辑器** 中可以看到不同模式对应的网段, 可以修改. 如图举例, 虚拟机选择不同网络练级模式,对应的ip 设置:

* 当虚拟机设置为 NAT模式时, 虚拟的ip 需要设置为 192.168.145.\*, 
* 当虚拟机设置为 仅主机模式, 那么虚拟机ip 需要设置为 192.168.67.0;
* 当虚拟机设置为 桥接模式时, 那么虚拟机ip 需要设置为和本机真实ip 同一网段.
  ![](/assets/vm_network_2017-05-22_182516.png)

## 2. 设置虚拟机ip

### 2.1 设置虚拟机网络连接方式

可以通过点击 **编辑虚拟机设置** 修改参数, 由于默认就是NAT 方式, 所以没有必要修改, 直接点击 **开启此虚拟机** 即可  
![](/assets/vm_network_2017-05-22_184149.png)

### 2.2 使用root 用户登录虚拟机系统, 并执行setup 命令

![](/assets/vm_network_2017-05-22_184432.png)

#### 2.2.1 通过setup 图形页面,设置ip

通过键盘上下键移动,选择第三项 **Network configuration**, 点击 **Enter**

![](/assets/vm_network_2017-05-22_184457.png)

#### 2.2.2 选择 Device configuration, 点击Enter

![](/assets/vm_network_2017-05-22_184507.png)

#### 2.2.3 选择网卡, 直接按Enter

![](/assets/vm_network_2017-05-22_184536.png)

#### 2.2.4 设置ip, 点击OK

先将 **Sstatic IP** 的\* 按空格去掉, 然后设置 ip, 子网掩码, 网关, 如果不去掉则是动态获取ip. 服务器不推荐使用动态获取ip 的方式,因为服务器通常都是通过远程连接工具来操作的, 若没有固定ip ,是没有办法进行远程连接的. 注意此处的Name 和 Device 不要修改, 这个名称为网络配置文件的文件名.参看 2.3  
![](/assets/vm_network_2017-05-22_184601.png)

#### 2.2.5 按Tab 键,选择保存, 按Enter

![](/assets/vm_network_2017-05-22_184638.png)

#### 2.2.6 按Tab 键, 选择 Save&Quit , 按Enter

![](/assets/vm_network_2017-05-22_184736.png)

#### 2.2.7 按Tab 键,选择Quit, 按Enter

![](/assets/vm_network_2017-05-22_184801.png)

### 2.5 修改网络配置文件

新装的linux 系统, 通过setup 设置完ip 之后, 还需要修改网络配置文件: **/etc/sysconfig/network-scripts/ifcfg-eth0**, 只需要将ONBOOT 设置为yes 即可.\(不同电脑不一定都是ifcfg-eth0 , 得看图2.2.4 中的Device 名称.\)

编辑:

```bash
[root@localhost opt]# vim /etc/sysconfig/network-scripts/ifcfg-eth0
```

配置文件:/etc/sysconfig/network-scripts/ifcfg-eth0

---

DEVICE=eth0  
HWADDR=00:0c:29:fc:81:30  
TYPE=Ethernet  
UUID=1b4c2a48-3bab-4dc9-aaf3-4911d1f8e19e  
**ONBOOT=yes**  
NM\_CONTROLLED=yes  
BOOTPROTO=none  
IPADDR=192.168.145.100  
NETMASK=255.255.255.0  
GATEWAY=192.168.145.0  
USERCTL=no  
PEERDNS=yes  
IPV6INIT=no

---

### 2.6 重启网络服务

![](/assets/vm_network_2017-05-22_185444.png)

## 3. 远程连接工具

### 3.1 远程命令执行工具

linux 命令远程连接工具有很多, 比如说putty, SecureCRT, XShell 等, 笔者习惯使用 SecureCRT 和putty, 此处仅演示SecureCRT 的使用方式. SecureCRT 安装方式就不讲了,网上多的是.

#### 3.1.1 打开SecureCRT, 依次点击图中按钮

第一个红框里面的按钮为在当前窗口中打开新的会话,以后最常用的按钮.  
![](/assets/vm_scrt_2017-05-22_192928.png)

#### 3.1.2 默认使用SSH2协议, 点击下一步

![](/assets/vm_scrt_2017-05-22_192134.png)

#### 3.1.3 输入主机ip, 端口号, 用户名, 点击下一步

![](/assets/vm_scrt_2017-05-22_192207.png)

#### 3.1.4 默认选择 SFTP协议, 点击下一步

![](/assets/vm_scrt_2017-05-22_192226.png)

#### 3.1.5 修改会后名称,建议使用ip@username 格式, 点击完成

![](/assets/vm_scrt_2017-05-22_192250.png)

#### 3.1.6 选择刚刚新建的会话, 点击连接

![](/assets/vm_scrt_2017-05-22_192306.png)

#### 3.1.7 点击接受并保存

第一连接会弹出此提示,以后就不会弹出了  
![](/assets/vm_scrt_2017-05-22_192319.png)

#### 3.1.8 输入密码, 点击确定

![](/assets/vm_scrt_2017-05-22_192352.png)

#### 3.1.9 登录成功

![](/assets/vm_scrt_2017-05-22_192413.png)

#### 3.1.10 常用按钮

* 第一个按钮为选择连接按钮, 在当前窗口中平铺打开新的谅解
* 第二个按钮为重新连接按钮, 当连接断开时,可使用

![](/assets/vm_scrt_2017-05-22_193844.png)

### 3.2 远程文件上传下载工具
本地与远程服务器之间进行文件传输是必不可少的操作, 我们可以通过Wincp 进行操作. Wincp 不仅可以做为linux 系统与本地的文件交换工具, 还可以连接ftp 服务器.
* 连接linux 服务器, 使用sftp 协议, 默认端口22
* 连接ftp 服务器, 使用ftp 协议, 默认端口21

#### 3.2.1 新建sftp 连接
1. 选择新建站点, 选择SFTP 协议, 输入ip 端口号, 用户名, 密码等参数, 点击保存, 弹出提示框
2. 重命名会话名称, 点击保存密码, 点击确定即可
![](/assets/vm_Wincp_2017-05-22_195202.png)

#### 3.2.2 连接服务器
双击左侧列表即可连接, 连接之后左边为本地文件, 右侧为服务器文件, 通过鼠标点击文件左右拖拽即可实现从服务器上传下载操作.
![](/assets/vm_Wincp_2017-05-22_195820.png)



