# YubiKey 设置备忘录

本文档记录了我配置 YubiKey 的过程，此文当适用于我的设备：MacBook Pro 、 YubiKey 5C NFC。

# 购买 YubiKey

建议在官网购买

- https://www.yubico.com/sg/store

访问 [yubico.com/genuine](https://www.yubico.com/genuine/) 来 [验证 YubiKey](https://support.yubico.com/hc/en-us/articles/360013723419-How-to-Confirm-Your-Yubico-Device-is-Genuine) 是否为正品。

# 安装软件
通过 [HomeBrew](https://brew.sh/) 安装以下软件包
```
brew install gnupg yubikey-personalization ykman pinentry-mac
```

# 重置
`ykman fido reset` 此命令将重置 FIDO U2F 凭据，中途需要重新插入 YubiKey 并触摸。
```
❯ ykman fido reset
WARNING! This will delete all FIDO credentials, including FIDO U2F credentials, and restore factory settings. Proceed? [y/N]: y
Remove and re-insert your YubiKey to perform the reset...
Touch your YubiKey...
```

`ykman oath reset` 此命令将重置 OATH 账户
```
❯ ykman oath reset
WARNING! This will delete all stored OATH accounts and restore factory settings. Proceed? [y/N]: y
Resetting OATH data...
Success! All OATH accounts have been deleted from the YubiKey.
```

`ykman openpgp reset` 此命令将重置 YubiKey OpenPGP 数据
```
❯ ykman openpgp reset
WARNING! This will delete all stored OpenPGP keys and data and restore factory settings. Proceed? [y/N]: y
Resetting OpenPGP data, don't remove the YubiKey...
Success! All data has been cleared and default PINs are set.
PIN:         123456
Reset code:  NOT SET
Admin PIN:   12345678
```

`ykman piv reset` 此命令将重置 PIV 数据
```
❯ ykman piv reset
WARNING! This will delete all stored PIV data and restore factory settings. Proceed? [y/N]: y
Resetting PIV data...
Success! All PIV data have been cleared from the YubiKey.
Your YubiKey now has the default PIN, PUK and Management Key:
	PIN:	123456
	PUK:	12345678
	Management Key:	010203040506070801020304050607080102030405060708
```

删除 `.gnupg` 文件夹，如果您已经有gpg密钥，请将它们备份到某个地方。
```
rm -rf ~/.gnupg
```
# 生成 gpg 密钥
## 生成主密钥和子密钥
- 密钥有 4 种类型：S、C、E 和 A。S 用于签名，C 用于认证，E 用于加密，A 用于身份验证。
- gpg默认过程将为 S 和 C 生成主密钥，为 E 生成子密钥。
- 注意：电子邮件地址必须设置为与您的 GitHub 帐户相同的电子邮件地址。否则，您无法使用 GPG 签署提交。

```
❯ gpg --expert --full-gen-key

gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: 目录‘/Users/ian/.gnupg’已创建
请选择您要使用的密钥类型：
   (1) RSA 和 RSA
   (2) DSA 和 Elgamal
   (3) DSA（仅用于签名）
   (4) RSA（仅用于签名）
   (7) DSA（自定义用途）
   (8) RSA（自定义用途）
   (9) ECC（签名和加密） *默认*
  (10) ECC（仅用于签名）
  (11) ECC（自定义用途）
  (13) 现有密钥
 （14）卡中现有密钥
您的选择是？ 8

RSA 密钥的可实现的功能： 签名（Sign） 认证（Certify） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 签名（Sign） 认证（Certify） 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ s

RSA 密钥的可实现的功能： 签名（Sign） 认证（Certify） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 认证（Certify） 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ e

RSA 密钥的可实现的功能： 签名（Sign） 认证（Certify） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 认证（Certify）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ q
RSA 密钥的长度应在 1024 位与 4096 位之间。
您想要使用的密钥长度？(3072) 4096
请求的密钥长度是 4096 位
请设定这个密钥的有效期限。
         0 = 密钥永不过期
      <n>  = 密钥在 n 天后过期
      <n>w = 密钥在 n 周后过期
      <n>m = 密钥在 n 月后过期
      <n>y = 密钥在 n 年后过期
密钥的有效期限是？(0) 0
密钥永远不会过期
这些内容正确吗？ (y/N) y

GnuPG 需要构建用户标识以辨认您的密钥。

真实姓名： Ian
电子邮件地址： the@niceboy.org
注释：
您选定了此用户标识：
    “Ian <the@niceboy.org>”

更改姓名（N）、注释（C）、电子邮件地址（E）或确定（O）/退出（Q）？ o
我们需要生成大量的随机字节。在质数生成期间做些其他操作（敲打键盘
、移动鼠标、读写硬盘之类的）将会是一个不错的主意；这会让随机数
发生器有更好的机会获得足够的熵。
gpg: /Users/ian/.gnupg/trustdb.gpg：建立了信任度数据库
gpg: 目录‘/Users/ian/.gnupg/openpgp-revocs.d’已创建
gpg: 吊销证书已被存储为‘/Users/ian/.gnupg/openpgp-revocs.d/96473E3A73C65207B6A54C2F716CF6A48C7B5249.rev’
公钥和私钥已经生成并被签名。

pub   rsa4096 2024-03-23 [C]
      96473E3A73C65207B6A54C2F716CF6A48C7B5249
uid                      Ian <the@niceboy.org>
```
## 添加签名密钥
```
gpg> addkey
请选择您要使用的密钥类型：
   (3) DSA（仅用于签名）
   (4) RSA（仅用于签名）
   (5) ElGamal（仅用于加密）
   (6) RSA（仅用于加密）
   (7) DSA（自定义用途）
   (8) RSA（自定义用途）
  (10) ECC（仅用于签名）
  (11) ECC（自定义用途）
  (12) ECC（仅用于加密）
  (13) 现有密钥
 （14）卡中现有密钥
您的选择是？ 8

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 签名（Sign） 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ e

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 签名（Sign）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ q
RSA 密钥的长度应在 1024 位与 4096 位之间。
您想要使用的密钥长度？(3072) 4096
请求的密钥长度是 4096 位
请设定这个密钥的有效期限。
         0 = 密钥永不过期
      <n>  = 密钥在 n 天后过期
      <n>w = 密钥在 n 周后过期
      <n>m = 密钥在 n 月后过期
      <n>y = 密钥在 n 年后过期
密钥的有效期限是？(0) 0
密钥永远不会过期
这些内容正确吗？ (y/N) y
真的要创建吗？(y/N) y
我们需要生成大量的随机字节。在质数生成期间做些其他操作（敲打键盘
、移动鼠标、读写硬盘之类的）将会是一个不错的主意；这会让随机数
发生器有更好的机会获得足够的熵。

sec  rsa4096/716CF6A48C7B5249
     创建于：2024-03-23  有效至：永不       可用于：C
     信任度：绝对        有效性：绝对
ssb  rsa4096/A09E7A9C08408F73
     创建于：2024-03-23  有效至：永不       可用于：SR
[ 绝对 ] (1). Ian <the@niceboy.org>
```
## 添加加密密钥
```
gpg> addkey
请选择您要使用的密钥类型：
   (3) DSA（仅用于签名）
   (4) RSA（仅用于签名）
   (5) ElGamal（仅用于加密）
   (6) RSA（仅用于加密）
   (7) DSA（自定义用途）
   (8) RSA（自定义用途）
  (10) ECC（仅用于签名）
  (11) ECC（自定义用途）
  (12) ECC（仅用于加密）
  (13) 现有密钥
 （14）卡中现有密钥
您的选择是？ 8

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 签名（Sign） 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ s

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ q
RSA 密钥的长度应在 1024 位与 4096 位之间。
您想要使用的密钥长度？(3072) 4096
请求的密钥长度是 4096 位
请设定这个密钥的有效期限。
         0 = 密钥永不过期
      <n>  = 密钥在 n 天后过期
      <n>w = 密钥在 n 周后过期
      <n>m = 密钥在 n 月后过期
      <n>y = 密钥在 n 年后过期
密钥的有效期限是？(0) 0
密钥永远不会过期
这些内容正确吗？ (y/N) y
真的要创建吗？(y/N) y
我们需要生成大量的随机字节。在质数生成期间做些其他操作（敲打键盘
、移动鼠标、读写硬盘之类的）将会是一个不错的主意；这会让随机数
发生器有更好的机会获得足够的熵。

sec  rsa4096/716CF6A48C7B5249
     创建于：2024-03-23  有效至：永不       可用于：C
     信任度：绝对        有效性：绝对
ssb  rsa4096/A09E7A9C08408F73
     创建于：2024-03-23  有效至：永不       可用于：SR
ssb  rsa4096/857D983C0DFF46DC
     创建于：2024-03-23  有效至：永不       可用于：ER
[ 绝对 ] (1). Ian <the@niceboy.org>
```
## 添加身份认证密钥
```
gpg> addkey
请选择您要使用的密钥类型：
   (3) DSA（仅用于签名）
   (4) RSA（仅用于签名）
   (5) ElGamal（仅用于加密）
   (6) RSA（仅用于加密）
   (7) DSA（自定义用途）
   (8) RSA（自定义用途）
  (10) ECC（仅用于签名）
  (11) ECC（自定义用途）
  (12) ECC（仅用于加密）
  (13) 现有密钥
 （14）卡中现有密钥
您的选择是？ 8

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 签名（Sign） 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ s

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 加密（Encrypt）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ e

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能：

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ a

RSA 密钥的可实现的功能： 签名（Sign） 加密（Encrypt） 身份验证（Authenticate）
目前启用的功能： 身份验证（Authenticate）

   (S) 签名功能开关
   (E) 加密功能开关
   (A) 身份验证功能开关
   (Q) 已完成

您的选择是？ q
RSA 密钥的长度应在 1024 位与 4096 位之间。
您想要使用的密钥长度？(3072) 4096
请求的密钥长度是 4096 位
请设定这个密钥的有效期限。
         0 = 密钥永不过期
      <n>  = 密钥在 n 天后过期
      <n>w = 密钥在 n 周后过期
      <n>m = 密钥在 n 月后过期
      <n>y = 密钥在 n 年后过期
密钥的有效期限是？(0) 0
密钥永远不会过期
这些内容正确吗？ (y/N) y
真的要创建吗？(y/N) y
我们需要生成大量的随机字节。在质数生成期间做些其他操作（敲打键盘
、移动鼠标、读写硬盘之类的）将会是一个不错的主意；这会让随机数
发生器有更好的机会获得足够的熵。

sec  rsa4096/716CF6A48C7B5249
     创建于：2024-03-23  有效至：永不       可用于：C
     信任度：绝对        有效性：绝对
ssb  rsa4096/A09E7A9C08408F73
     创建于：2024-03-23  有效至：永不       可用于：SR
ssb  rsa4096/857D983C0DFF46DC
     创建于：2024-03-23  有效至：永不       可用于：ER
ssb  rsa4096/5AA410FBB65B7CF1
     创建于：2024-03-23  有效至：永不       可用于：AR
[ 绝对 ] (1). Ian <the@niceboy.org>
```
# 将密钥传输至 YubiKey
```
gpg --edit-key $GPG_KEY_ID

gpg> keytocard
Really move the primary key? (y/N) y
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

# input passphrase and PIN

gpg> key 1  # select E key
gpg> keytocard
Please select where to store the key:
   (2) Encryption key
Your selection? 2

# input passphrase and PIN

gpg> key 1  # unselect E key
gpg> key 2  # select A key
gpg> keytocard
Please select where to store the key:
   (3) Authentication key
Your selection? 3

gpg> save
```
# 验证 Key
```
gpg --card-status
```





# 参考资料

- https://github.com/drduh/YubiKey-Guide
- https://github.com/susumuota/yubikey-setup
- https://github.com/techprober/yubikey-reference
- https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP
- https://www.yafa.moe/post/yubikey-guide