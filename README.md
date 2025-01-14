欢迎来到Lean的Openwrt源码仓库！
=

[English](./README_EN.md)

如何编译自己需要的 OpenWrt 固件
-
注意：
-
1. **不**要用 **root** 用户进行编译！！！
2. 国内用户编译前最好准备好梯子
3. 默认登陆IP 10.10.10.250 密码 password


编译命令如下:
-
1. 首先装好 Ubuntu 64bit，推荐 Ubuntu 20.04 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入
   `
   sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync
   `

3. 使用 `git clone https://github.com/yordon96/lean_code_openwrt` 命令下载好源代码，然后 `cd lean_code_openwrt` 进入目录

4. ```bash
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

5. `make -j8 download V=s` 下载dl库（国内请尽量全局科学上网）

6. 输入 `make -j1 V=s` （-j1 后面是线程数。第一次编译推荐用单线程）即可开始编译你要的固件了。

本套代码保证肯定可以编译成功。里面包括了 R21 所有源代码，包括 IPK 的。
-
二次编译(仅升级，不修改配置）：
```bash
cd lean_code_openwrt
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

如果需要重新配置：
```bash
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```

编译完成后输出路径：bin/targets

如果你使用WSL或WSL2进行编译：
------
由于wsl的PATH路径中包含带有空格的Windows路径，有可能会导致编译失败，请在将make -j1 V=s或make -j$(($(nproc) + 1)) V=s改为

首次编译：
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j1 V=s 
```
二次编译：
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j$(($(nproc) + 1)) V=s
```
开启IPV6：
----
选上extra packages——ipv6helper
在 Network – Firewall – ip6tables 下启用 ip6tables-extra 和 ip6tables-mod-nat 项。

更改LAN口的默认IP地址：
----
````bash
cd lean_code_openwrt
vim package/base-files/files/bin/config_generate
````
大概在150行找到我们默认的原IP地址（10.10.10.250），按“i”把对应的IP更改即可然后按shift+: 输入wq回车保存退出
***
编译丰富插件时，建议修改下面两项默认大小，留足插件空间。（ x86/64 ）！！！
- Target Images ---> (16) Kernel partition size (in MB)   #默认是 (16) 建议修改 (256)
- Target Images ---> (160) Root filesystem partition size (in MB) #默认是 (160) 建议修改 (512)

自定义固件版本信息：
----
````bash
package/lean/default-settings/files/zzz-default-settings
````
找到DISTRIB_REVISION在单引号之内可添加任何信息
