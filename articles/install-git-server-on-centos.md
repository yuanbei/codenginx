之前我在学习git时主要是掌握了git客户端相关的操作。最近由于需要，自己搭建了一个git server，故将搭建过程记录下来。我的服务器是Cent OS 64位，其中大部分命令在类linux 系统都适用。

搭建过程主要分以下三个大的步骤。

###为git server 创建并配置git用户,用户组。
1.切换到root 用户

```shell
su root
```

2.创建git 组

```shell
groupadd git
groupadd user
```
3.添加git账户

```shell
useradd git -g git -m -s /bin/bash
usermod -G git,user git
passwd git #设置密码
```
4.将git用户添加到sudoers中去

```shell
vim /etc/sudoers
git ALL=(ALL) ALL #在文件最后一行加上
```
###准备好git仓库
1.安装git 所需的程序

```shell
sudo yum -y install git git-core
```
2.切换到git用户

```shell
su git
cd /home/git/ #切换到git用户所在的用户目录
```

3.在git用户目录下创建或者clone 一个git代码仓库

```shell
git clone https://github.com/yuanbei/AdsCrawler.git #我这里选择clone
```
4.配置代码仓库

```shell
cd /home/git/AdsCrawler
git config core.repositoryformatversion 0
git config core.filemode true
git config core.bare true
git config receive.denyCurrentBranch ignore #允许用户提交代码
```

###安装并配置好SSH服务器
1.安装SSH服务器所需的软件包

```shell
sudo yum -y install openssl ssh-server
```

2.配置并启动SSH 服务器

```shell
chkconfig sshd on
service sshd start
```

3.保证服务器上22端口是打开的

```shell
netstat -tulpn | grep :22 #查看22端口侦听情况
#设置系统防火墙
vi /etc/sysconfig/iptables #在文件最后添加一行
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
service iptables restart #重启服务器
```
4.添加用户密钥

```shell
mkdir /home/git/.ssh
vim /home/git/.ssh/authorized_keys #创建文件，并逐一添加用户的公钥
```

###客户端访问
客户端可通过如下命令获取到代码，并提交代码到仓库中。

```shell
git clone git@host:/home/git/AdsCrawler #host可是ip也可以是域名
```

###参考资料
1.[如何生成管理RSA密钥](https://help.github.com/articles/generating-ssh-keys/)

2.[搭建自己的git代码服务器](http://blog.chinaunix.net/uid-27105712-id-4511124.html)

3.[安装和配置SSH](http://www.cyberciti.biz/faq/centos-ssh/)
