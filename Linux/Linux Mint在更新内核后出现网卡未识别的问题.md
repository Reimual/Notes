# Linux Mint在更新内核后出现网卡未识别的问题

&emsp;

前段时间在U盘中安装了一个Linux Mint，但后来因为更新了一下内核版本导致无线网卡无法识别（network UNCLAIMED），问了deepseek和chatgpt后（进入Live模式给新内核的Linux Mint安装网卡驱动、systemctl start/stop NetworkManager无数次、查看防火墙、甚至使用了手机usb网络连接等）均无果，后来在b站一名大佬（额呵呵好好的哈）的文栏下面找到了解决方案：

&emsp;

1. 进入GRUB，选择旧内核版本
2. 查看网络状态（无线网卡在旧版本下可以识别，且网络状态正常）
3. 重启，进入GRUB选择Recovery状态的旧内核版本
4. 点击Enable networking和fix broken packages并等待运行结束
5. reboot重启，再次选择新内核版本启动，此时网卡识别正常，网络状态正常

&emsp;

# 教训

&emsp;

如果不是特别需求的话，尽量不要频繁更新内核版本，以免因为各种未知错误（主要集中在驱动问题上）影响日常的工作学习