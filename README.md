# CopilotWindowsPC-DisableVBS-Guidance
一个关于如何完全关闭基于虚拟化的安全性(VBS)的帮助文档，旨在解决ACE虚拟化无法启用、VMware提示不支持嵌套的Intel VT/x、各种编译器（尤其是Java）效率低下的问题  
由于微软CopilotPC认证收紧了设备安全性和开放性，且最新专业版引入了新的安全机制用于配合AI组件，且本人没有找到更好的系统的教程，故经两天不懈熬夜摸索，消耗若干头发和抽纸~~（和一个白洲梓）~~，成功在Windows11 Pro上关闭了基于虚拟化的安全性。  
<img width="1246" height="129" alt="image" src="https://github.com/user-attachments/assets/c0a53765-62f5-41a9-8dae-7038a25ac4ab" />  
***注意：只有Windows 专业版/教育版/LTS及以上版本才可关闭VBS！Windows CountrySpecific(典型：Win11家庭中文版)/Home等版本都不行！***
## A.准备工作
### 1.更改BIOS设置以规避硬件问题
由于HugeHard要求~~(勒令)~~新电脑引入大量基于虚拟化的安全性的硬件特性，所以为了人类的使用体验，大部分BIOS厂商都内置关闭特性的方法，以AMI BIOS为例：  
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
