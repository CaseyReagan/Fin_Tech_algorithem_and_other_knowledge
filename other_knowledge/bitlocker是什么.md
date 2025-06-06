#  BitLocker是什么要注意什么
本文借鉴：1.https://learn.microsoft.com/zh-cn/windows/security/operating-system-security/data-protection/bitlocker/#device-encryption
   
### （一）背景
公司新的一套用的微软全家桶，在入职一周后遇到个恶性Bug，公司admin设置是每周五会reboot，在经历一次reboot后，我的个人本地用户失去了所有我自己创建的文件夹的全面权限。IT Admin折腾了很久，最后必须给两个磁盘上bitlock再初始化本地用户分组。
   
### （二）BitLocker到底是什么
BitLocker是微软系统自带的，从Win Vista开始的一项‌全盘加密安全功能，用 ‌AES 加密算法。
+ 【功能】：主要是保硬盘的，确保物理的硬盘丢失后也无法被解密打开。
+ 【使用】：只能以磁盘为单位加密，不能加密单独文件夹。
+ 【免费？】：家庭版win没有，只有专业版自带并且免费。
   
### （三）使用注意点
磁盘解锁后不影响任何的文件传输,远程桌面等使用。
   
### （四）BitLocker 加密与 Windows 账户权限的深层联动机制‌
根本原因：企业安全策略的强制绑定‌合规性要求（如 ISO 27001/NIST）：
   
许多行业规定（金融、医疗），‌未加密设备不允许访问公司域资源‌。账户可能被加入 Active Directory (AD) 域，但电脑因未加密被标记为“不合规设备”，触发权限降级。
   
+ 现象：无法修改自建文件夹，实则是系统自动限制“非合规设备”的写权限。