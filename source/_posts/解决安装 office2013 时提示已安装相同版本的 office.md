---
title: 解决安装 office2013 时提示已安装相同版本的 office
date: 2016-03-24 03:00:00
tags: office
categories: 技术
---
  
## 问题:
![](http://images2015.cnblogs.com/blog/896608/201603/896608-20160324025635151-1558617525.jpg)

在尝试使用官方的卸载清理工具无果后，

终极解决办法还是**删除注册表项**：【注意：只针对 **office 2013**】
 
```
HKEY_CURRENT_USER\Software\Microsoft\Office\15.0

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Office\15.0

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Office\Delivery\SourceEngine\Downloads\* 0FF1CE *

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\ * 0FF1CE *

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\Upgrade Codes\ * F01FEC

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Products\ * F01FEC

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\ose

HKEY_CLASSES_ROOT\Installer\Features\ * F01FEC

HKEY_CLASSES_ROOT\Installer\Products\ * F01FEC

HKEY_CLASSES_ROOT\Installer\UpgradeCodes\ * F01FEC

HKEY_CLASSES_ROOT\Installer\Win32Assemblies\ * Office15 *
```

---

#### Reference：

[office2013 如何完全卸载 office2013 官方完全卸载教程 ](http://www.jb51.net/office/73477.html)