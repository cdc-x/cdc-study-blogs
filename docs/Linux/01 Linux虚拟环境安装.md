# Linux 虚拟环境安装

### 一、安装虚拟机

现在各家厂商云服务器价格越来越高的离谱，我们在本地开发、测试或者学习阶段，使用云服务器成本较高。推荐使用虚拟机，在 `windows` 环境上创建虚拟的 `linux` 环境使用。虚拟机软件种类有很多，个人还是比较推荐使用 `VMware Workstation` 。

直接百度搜索或者去官网 https://www.vmware.com/ 就能下载，下载完成之后直接傻瓜式安装，安装完成可以看到以下界面：

<img src="./static/img/linux/1.png" style="zoom: 50%;" /> 

从下面密钥中任选一条，对虚拟机进行注册激活后即可使用：

```shell
UG5J2-0ME12-M89WY-NPWXX-WQH88
GA590-86Y05-4806Y-X4PEE-ZV8E0
YA18K-0WY8P-H85DY-L4NZG-X7RAD
UA5DR-2ZD4H-089FY-6YQ5T-YPRX6
B806Y-86Y05-GA590-X4PEE-ZV8E0
ZF582-0NW5N-H8D2P-0XZEE-Z22VA
```

 <img src="./static/img/linux/2.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/3.png" style="zoom: 50%;" /> 

### 二、下载系统镜像

官网系统镜像下载速度太慢了，建议使用国内的几个镜像源：

- 阿里镜像源：https://developer.aliyun.com/mirror/
- 网易镜像源：https://mirrors.163.com/
- 搜狐镜像源：https://mirrors.sohu.com/

以阿里镜像源下载 `CentOS` 系统镜像为例：

- 第一步，访问阿里镜像源首页，选择 `CentOS` 系统，跳转到对应的下载页面

<img src="./static/img/linux/4.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/5.png" style="zoom: 50%;" /> 

- 第二步，选择系统版本，并依次进入子级目录，找到对应的镜像文件。文件中框出来的部分，第一个为镜像文件，下载可能起来比较慢，第二个为种子文件，可以搭配迅雷等工具使用，下载速度较快。

<img src="./static/img/linux/6.png" style="zoom: 50%;" /> 

### 三、创建虚拟机

- 打开虚拟机软件，点击创建新的虚拟机

<img src="./static/img/linux/7.png" style="zoom: 50%;" /> 



- 选择自定义安装

<img src="./static/img/linux/8.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/9.png" style="zoom: 50%;" /> 



- 选择稍后安装操作系统

<img src="./static/img/linux/10.png" style="zoom: 50%;" /> 



- 现在系统类型为 `Linux`，系统版本为 `CentOS`，版本号要和下载的镜像保持一致

<img src="./static/img/linux/11.png" style="zoom: 50%;" /> 



- 设置虚拟机名字（也可以不设置，使用默认的），选择虚拟机安装的位置，默认存在C盘，建议换一个空间比较大的其他盘

<img src="./static/img/linux/12.png" style="zoom: 50%;" /> 



- 给虚拟机分配处理器配置，根本实际需求设置即可

<img src="./static/img/linux/13.png" style="zoom: 50%;" /> 



- 给虚拟机分配内存，根据实际需求分配即可

<img src="./static/img/linux/14.png" style="zoom: 50%;" /> 



- 设置虚拟机网络，选择 `NAT` 方式。桥接模式下，虚拟机会直接跟外网进行交互，因此必须要有一个独立的 IP；`NAT` 模式下，虚拟机和外网交互时，会通过 windows 做一个中转，换而言之该模式下所有虚拟机的 IP 都是 windows 进行分配管理的，和外网交互的实际上还是 windows

<img src="./static/img/linux/15.png" style="zoom: 50%;" /> 



- 设置虚拟机 IO 控制器和磁盘类型，直接按推荐的来即可，配置完成后，选择创建新的虚拟磁盘

<img src="./static/img/linux/16.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/17.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/18.png" style="zoom: 50%;" /> 



- 给虚拟机分配磁盘大小，根据实际需求来即可。不要勾选 “立即分配所有磁盘空间”，否则将会直接将 windows 实际的物理大小磁盘分配给虚拟机。选择 “将虚拟磁盘拆分成多个文件”，这样虽然配置上看上去给虚拟机分配了 20GB，实际上 windows 会根据虚拟机实际使用的大小来动态分配。分配好磁盘大小后，可以选择设置磁盘文件名（也可以使用默认名），必须是 `.vmdk` 结尾

<img src="./static/img/linux/19.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/20.png" style="zoom: 50%;" /> 



- 选择自定义硬件，选择使用下载好的系统镜像进行安装。镜像选择完成后，点击关闭，返回上一层向导界面，点击完成

<img src="./static/img/linux/21.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/22.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/23.png" style="zoom: 50%;" /> 



- 所有配置完成后会生成一台空的虚拟机，此时点击开启虚拟机，进行系统安装

<img src="./static/img/linux/24.png" style="zoom: 50%;" /> 



- 点击黑屏区域，鼠标就进入了虚拟机中（按 `ctrl` + `alt` 可以退出鼠标），通过键盘向上的箭头，选中 `install CentOS 7` ，再按回车

<img src="./static/img/linux/25.png" style="zoom: 50%;" /> 



- 等待一段时间后，出现系统相关配置，推荐选择系统安装向导语言为英文

<img src="./static/img/linux/26.png" style="zoom: 50%;" /> 



- 设置时区为上海时区，手动调整时间与本地保持一致

<img src="./static/img/linux/27.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/28.png" style="zoom: 50%;" /> 



- 设置系统带桌面功能（如果习惯终端模式，这一步可以跳过）

<img src="./static/img/linux/29.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/30.png" style="zoom: 50%;" /> 



- 设置系统磁盘分区，选择自动分区即可

<img src="./static/img/linux/31.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/32.png" style="zoom: 50%;" /> 



- 打开虚拟机网络

<img src="./static/img/linux/33.png" style="zoom: 50%;" />  

<img src="./static/img/linux/34.png" style="zoom: 50%;" /> 



- 继续完成安装

<img src="./static/img/linux/35.png" style="zoom: 50%;" /> 



- 设置 root 用户密码及设置登录用户，密码必须是强密码，即包含大小写字母、数字、特殊字符且不能包含弱口令（单词、连续数字、连续的键盘按键），长度必须8位以上。如果还是想使用弱密码，连续点击两次 `Done` 即可设置并退出

<img src="./static/img/linux/36.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/37.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/38.png" style="zoom: 50%;" /> 



- 点击重启

<img src="./static/img/linux/39.png" style="zoom: 50%;" /> 



- 重启完成后，确认用户协议，完成系统安装

<img src="./static/img/linux/40.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/41.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/42.png" style="zoom: 50%;" /> 



- 使用账号登录系统，进行最后的配置。系统语言选择位中文后，需要先退出当前账户，重新登录一次才能生效

<img src="./static/img/linux/43.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/44.png" style="zoom: 50%;" /> 

<img src="./static/img/linux/45.png" style="zoom: 50%;" /> 