# CopilotWindowsPC-DisableVBS-Guidance
一个关于如何完全关闭基于虚拟化的安全性(VBS)的帮助文档，旨在解决ACE虚拟化无法启用、VMware提示不支持嵌套的Intel VT/x、各种编译器（尤其是Java）效率低下的问题  
由于微软CopilotPC认证收紧了设备安全性和开放性，且最新专业版引入了新的安全机制用于配合AI组件，且本人没有找到更好的系统的教程，故经两天不懈熬夜摸索，消耗若干头发和抽纸~~（和一个白洲梓）~~，成功在Windows11 Pro上关闭了基于虚拟化的安全性。  
<img width="1246" height="129" alt="image" src="https://github.com/user-attachments/assets/c0a53765-62f5-41a9-8dae-7038a25ac4ab" />  
***注意：只有Windows 专业版/教育版/LTS及以上版本才可关闭VBS！Windows CountrySpecific(典型：Win11家庭中文版)/Home等版本都不行！***
## A.准备工作
### 1.更改BIOS设置以规避硬件问题
由于HugeHard要求(勒令)新电脑引入大量基于虚拟化的安全性的硬件特性，所以为了人类的使用体验，大部分BIOS厂商都内置关闭特性的方法，以AMI BIOS为例：  
<img width="1146" height="1123" alt="image" src="https://github.com/user-attachments/assets/3cc71a32-0d4c-4575-9cad-d2bbeea269b4" />  
### 2.调整Windows设置 避免不必要的麻烦
打开你的Windows安全中心，然后关闭以下所有开关：  
<img width="686" height="770" alt="image" src="https://github.com/user-attachments/assets/22473403-210e-43af-b684-e3835d64e4b1" />  
然后**重新启动**  
### 3.更换账户类型
打开设置>点击你的头像或“账户”>点击“你的信息”>查看Microsoft账户的可选操作是否为“停止自动登录到Microsoft应用”或“使用在线账户登陆”  
<img width="1517" height="192" alt="image" src="https://github.com/user-attachments/assets/fd77440a-299c-4896-bf48-eaf48c2a60ea" />  
若不是，请点击“使用本地账户登陆”，然后Windows会引导你设置用户名和密码，重新登陆。**（注意：此过程将清除所有已安装的证书和基于TPM储存信息的软件数据——例如Edge和Chrome的密码及历史记录等，待登陆后需要重新自行配置）**  
重置后**不要着急设置Windows Hello PIN或生物识别**，关闭虚拟化以后会失效！  
### 4.检查加密状态并解密电脑
完整且正常的关闭VBS前提是电脑硬盘内无任何Bitlocker加密数据，转到设置>隐私和安全性>设备加密，然后关闭这个降低系统性能和提供不可维修性的开关：  
<img width="1599" height="633" alt="image" src="https://github.com/user-attachments/assets/336e17b5-72c7-4e3a-81a1-eb841ccd0b60" />  
Windows设置中无法查看详细的解密状态，可以打开管理员权限的终端，执行'manage-bde -status'，当看到类似于以下的输出时，证明您已经完全解密了：  
```  
  BitLocker 驱动器加密: 配置工具版本 10.0.26100
  版权所有 (C) 2013 Microsoft Corporation。保留所有权利。
  
  可以使用 BitLocker 驱动器加密
  保护的磁盘卷:
  卷 D: [DATA]
  [数据卷]
  
      大小:              1907.73 GB
      BitLocker 版本:    无
      转换状态:          完全解密
      已加密百分比:      0.0%
      加密方法:          无
      保护状态:          保护关闭
      锁定状态:          已解锁
      标识字段:          无
      自动解锁:          已禁用
      密钥保护器:        找不到
  
  卷 C: [OS]
  [OS 卷]
  
      大小:              953.76 GB
      BitLocker 版本:    无
      转换状态:          完全解密
      已加密百分比:      0.0%
      加密方法:          无
      保护状态:          保护关闭
      锁定状态:          已解锁
      标识字段:          无
      密钥保护器:        找不到
```
### 5.配置PowerShell并下载微软准备好的脚本  
点击["Device Guard and Credential Guard hardware readiness tool"](https://www.microsoft.com/en-us/download/details.aspx?id=53337)，下载微软的ps1脚本并解压  
然后在这里，关闭PowerShell的脚本签名行为（在旧版系统中，此设置在“开发者选项”中）：  
<img width="1045" height="897" alt="image" src="https://github.com/user-attachments/assets/b3245c78-7e6e-448f-9678-1cd78c6320e9" />  

**至此，恭喜你已经完成了所有准备工作**  
******
## B.配置组策略以禁止启动DG/CG
打开“运行”，然后输入'gpedit.msc'，然后找到：  
<img width="1801" height="574" alt="image" src="https://github.com/user-attachments/assets/32240a22-8de0-4361-b43f-2d458f797504" />  
禁用：  
<img width="1026" height="949" alt="image" src="https://github.com/user-attachments/assets/2acd7b93-b461-4077-a232-bac1ba46a997" />  
## C.更改引导BCD
打开具有管理员权限的终端，然后：  
```
bcdedit /set hypervisorlaunchtype off
```  
再次键入'bcdedit'查看输出，如果和我一样在最后一条处看到了新加入的选项，证明操作成功完成。  
```
Windows 启动加载器
-------------------
标识符                  {current}
device                  partition=C:
path                    \Windows\system32\winload.efi
description             Windows 11
locale                  zh-CN
inherit                 {bootloadersettings}
isolatedcontext         Yes
allowedinmemorysettings 0x15000075
osdevice                partition=C:
systemroot              \Windows
resumeobject            {782bbc01-30d4-11f1-942c-b6964467d50b}
nx                      OptIn
bootmenupolicy          Standard
hypervisorlaunchtype    Off
```
## D.启动脚本
***注意！请务必退出所有安全软件！***
打开终端并'cd'到你解压的文件夹，然后
```
.\DG_Readiness_Tool_v3.6.ps1 -disable
```
在一系列夹杂着错误的输出中，脚本最后输出了'Please reboot the machine, for settings to be applied.'，此时重启电脑。（好了看来写到这只能暂时Commit changes了）


