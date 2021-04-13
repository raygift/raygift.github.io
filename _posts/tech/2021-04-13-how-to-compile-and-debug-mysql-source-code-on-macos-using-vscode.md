---
layout: post
title: 在MacOS从源码编译安装MySQL，并使用vscode 调试MySQL 源码
category: 技术
tags: MacOS,MySQL,VS Code
description: 总结一下如何在MacOS从源码编译安装MySQL，并使用vscode 调试MySQL 源码
---


操作系统：MacOS 11.2.2

MySQL源码版本：5.6.47

VS Code 版本：March 2021 (version 1.55)

## 编译安装

1. 下载mysql 源码：https://cdn.mysql.com//archives/mysql-5.6/mysql-5.6.47.tar.gz
2. 安装cmake 3.20.1，并设置cmake 命令行命令
3. 在mysql 源码根目录执行编译变量配置

```
BUILD/compile-pentium-debug --prefix=$HOME/mysql-bin
```

5. 在mysql 源码根目录执行编译

```
make && make install
```

### 设置的prefix 未生效

发现4.中指定安装目录为$HOME/mysql-bin 并没有生效，导致第5. 执行时仍然安装到了/usr/local/mysql ，且安装由于目录权限问题失败。

原本安装了mysql 8.0 到默认的/usr/local/mysql 路径，而mysql 5.6.47的部分文件已经替换了mysql 8.0 的文件

尝试卸载已有安装，并清理相关目录，参考https://blog.csdn.net/qq_40177015/article/details/111599464

6. 直接使用cmake而非BUILD 中的脚本，再次配置参数

```
cmake -DCMAKE_INSTALL_PREFIX=/Users/zpc/mysql5-6-47/mysql -DMYSQL_DATADIR=/Users/zpc/mysql5-6-47/mysql/data -DSYSCONFDIR=/Users/zpc/mysql5-6-47/etc -DMYSQL_USER=mysql -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_BOOST=boost
```

7. 执行编译安装 make && make install

8. 安装完成，创建 my.cnf 配置文件

   ```
   cd /Users/zpc/mysql5-6-47/mysql
   cp support-files/my-default.cnf /Users/zpc/mysql5-6-47/etc/my.cnf
   chown mysql:mysql /etc/my.cnf
   vi /Users/zpc/mysql5-6-47/etc/my.cnf
   [mysqld]
       basedir=/Users/zpc/mysql5-6-47/mysql
       datadir=/Users/zpc/mysql5-6-47/mysql/data
       #bind-address=0.0.0.0
       port=3306
       socket=/tmp/mysql.sock
       innodb_file_per_table=1
       default-storage-engine=INNODB
       explicit_defaults_for_timestamp=true
       symbolic-links=0
       max_connections=1000
       log-error=/Users/zpc/mysql5-6-47/mysql/mysql.log
       pid-file=/Users/zpc/mysql5-6-47/mysql/mysql.pid
   ```

   

   9. 初始化mysql 数据库与配置

   ```
   sudo -u mysql vim ./scripts/mysql_install_db # 修改.mysql_secret 保存目录
   
   ./scripts/mysql_install_db --user=mysql --basedir=/Users/zpc/mysql5-6-47/mysql --datadir=/Users/zpc/mysql5-6-47/mysql/data --defaults-file=/Users/zpc/mysql5-6-47/etc/my.cnf --random-passwords
   
   A RANDOM PASSWORD HAS BEEN SET FOR THE MySQL root USER !
   You will find that password in '/Users/zpc/mysql5-6-47/.mysql_secret'.
   ```

   ```
   
   ```

   ```
   sudo -u mysql cat /Users/zpc/mysql5-6-47/.mysql_secret   # 查看随机密码
   
   # The random password set for the root user at Tue Apr 13 17:52:47 2021 (local time): bXDTcTKo_vP3J5kc
   ```

10. 启动mysql 服务

```
sudo -u mysql ./etc/init.d/mysqld start

Starting MySQL
.. SUCCESS!
```

11. 连接MySQL

```
sudo -u mysql cat /Users/zpc/mysql5-6-47/.mysql_secret   # 查看随机密码

# The random password set for the root user at Tue Apr 13 17:52:47 2021 (local time): bXDTcTKo_vP3J5kc

/usr/local/mysql80/bin/mysql -uroot -pbXDTcTKo_vP3J5kc
mysql> select user,host from mysql.user;
ERROR 1820 (HY000): You must SET PASSWORD before executing this statement
```

12. 设置root 用户密码

```sql
mysql> SET PASSWORD = PASSWORD('123456');
```



## vscode 运行调试

前提条件：完成源码的编译，并运行成功

1. vscode 配置C++ 开发环境：

   1. 安装插件：C/C++、C/C++ Clang Command Adapter、CodeLLDB、Code Runner
   2. 打开mysql源码文件，配置c_cpp_properties.json、launch.json、tasks.json

2. 使用vscode 打开mysql 源码

3. command+shift+p，添加launch.json 配置，主要填写program及args，program为之前根据源码编译好的mysqld运行文件地址，args 为之前修改好的默认的mysql 运行文件

   ```
           "program": "/Users/zpc/mysql5-6-47/mysql/bin/mysqld", 
           "args": ["--defaults-file=/Users/zpc/mysql5-6-47/etc/my.cnf"],
   ```

4. 在vscode 的调试RUN AND DEBUG 页面，使用2. 中添加的调试配置启动

5. 若遇到权限问题，将mysql 编译安装的目录权限改为777



参考：

1. https://segmentfault.com/a/1190000021140392
2. https://blog.csdn.net/qq_40177015/article/details/111599464
3. https://blog.csdn.net/freedompuge/article/details/45335683
4. https://blog.csdn.net/sunhf_csdn/article/details/80229492
