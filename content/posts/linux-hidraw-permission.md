+++
title = 'Linux 下解决外设网页驱动问题'
date = 2025-09-01T17:00:00+08:00
toc = true
tags = ["Linux","udev"]
+++

部分外设有网页驱动，但是在 Linux 下并不能正常使用，是因为没有对应设备的权限，通过 `chrome://device-log` 可以看到 `FILE_ERROR_ACCESS_DENIED` 

即用户只有可读权限，没有写入权限。因此在这类的网页驱动中是能够显示设备，但是无法打开进行设置

![image](https://img.panp.cc/2025/09/7be62b15d57549da31289fcb34634fae.webp)

![image](https://img.panp.cc/2025/09/4ba526bb5b370ec23926a80e6bdd2a75.webp)

这时候可以通过手动赋予权限来进行设置（ X 为具体设备对应的值，在 chrome 的 log 中可以看到）

```
sudo chmod a+rw /dev/hidrawX
```


但是这样是临时的，在每次系统重启之后都需要重新手动授权

## 持久化授权

参考 [Google 开发者文档](https://developer.chrome.com/docs/capabilities/hid?hl=zh-cn) 的“开发者提示”部分：

>在大多数 Linux 系统上，HID 设备默认映射为只读权限。如需允许 Chrome 打开 HID 设备，您需要添加新的 udev 规则。在 /etc/udev/rules.d/50-yourdevicename.rules 中创建一个包含以下内容的文件：
>
>KERNEL=="hidraw*", ATTRS{idVendor}=="[yourdevicevendor]", MODE="0664", GROUP="plugdev"
>
>在上面的代码行中，如果您的设备是 Nintendo Switch Joy-Con 等，则 [yourdevicevendor] 为 057e。您还可以添加 ATTRS{idProduct} 以指定更具体的规则。确保您的 user 是 plugdev 群组的成员。然后，只需重新连接设备即可。


但是这样在 [Arch Wiki](https://wiki.archlinux.org/title/Udev#Allowing_regular_users_to_use_devices) 中是不推荐的做法，更推荐赋予当前登录用户权限，而不是整个用户组权限

并且实际上在 Arch 中并没有 `plugdev` 这个用户组，可以通过 `getent group` 命令验证

在 Wiki 中推荐使用 `uaccess` 的 `TAG`，uaccess 机制让 systemd-logind 自动为当前 `物理登录的桌面用户` 动态赋予设备 ACL 权限。
这样只有当前活跃桌面会话的用户拥有设备权限，其他用户（包括同一台机器的 SSH 用户）不会获得权限。

这样就不用再自己维护用户组，也不用给所有人开放 0666 权限


## 实现部分

### 创建 udev 规则

假设你的设备 idVendor 为 1234，idProduct 为 abcd

创建udev规则文件

```
sudo touch /etc/udev/rules.d/70-device.rules 
```

规则文件内容如下：

```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1234", ATTRS{idProduct}=="abcd", MODE="0660", TAG+="uaccess"
```

- MODE="0660" 只允许 root 和组访问，但实际权限将由 uaccess 控制。
- TAG+="uaccess" 让 systemd-logind 为当前物理会话用户动态添加 ACL。

刷新 udev 规则并重新插拔设备

```
sudo udevadm control --reload-rules
sudo udevadm trigger
```

此时应能正常读取设备信息并在网页驱动中进行配置。

![image](https://img.panp.cc/2025/09/847c54e6223cfaeab0d460fd10937ef9.webp)
![image](https://img.panp.cc/2025/09/21c38ce6b9fb57ef62f788fb79f896d4.webp)

可以选择执行下面这条命令检查当前用户是否有对应设备的权限

```
getfacl /dev/hidrawX
```

---

### 工具

Arch 安装 `usbutils` 来获取 `lsusb`

```
paru -S usbutils
```

然后使用 lsusb 获取当前设备信息来获取 idVendor 和 idProduct 

`:` 的前后即 idVendor 和 idProduct

![image](https://img.panp.cc/2025/09/82c29250d8e583c96ef72016b664f7a9.webp)

### udev规则 命名规范

udev 规则文件的命名基本是“随意的”，但有一些推荐规范：

- **数字前缀决定规则应用顺序**  
  文件名通常以两位或三位数字开头（如 70-），数字越小规则越早应用。数字后为自定义描述（如 70-device.rules）。

- **描述部分可自由命名**  
  `device` 可换为任意描述性内容，如 webusb、hidraw 等，只要便于自己识别即可。

- **结尾必须为 .rules**  
  这是 udev 识别规则文件的要求。

- **避免与已有规则文件名冲突**  
  不建议使用如 50、60、99 等标准规则文件前缀，以免被系统或包管理器覆盖。
