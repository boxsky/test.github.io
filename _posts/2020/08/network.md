### Linux虚拟机网卡配置

> 查看网卡当前ip,以及要修改的网卡名称

```bash
ifconfig
```
![linux虚拟机网卡信息](http://img.zhaobo.top/2020/08/network/ifconfig.png "linux虚拟机网卡信息")

> 进入目录/etc/sysconfig/network-scripts/,ls查看找到你要修改的网卡名称
![linux虚拟机网卡信息](http://img.zhaobo.top/2020/08/network/1.png "linux虚拟机网卡信息")

```bash
cd /etc/sysconfig/network-scripts/
vi ifcfg-xxx(根据你要修改的网卡名称查找) 
```
![linux虚拟机网卡信息](http://img.zhaobo.top/2020/08/network/2.png "linux虚拟机网卡信息")


> 修改完成保存后 进行重启网卡配置

```bash
service network restart
```
![linux虚拟机网卡信息](http://img.zhaobo.top/2020/08/network/3.png "linux虚拟机网卡信息")