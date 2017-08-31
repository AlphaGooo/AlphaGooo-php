
## 简述

php开发环境，需要连接oracle数据库，那么就需要配置oracle扩展。

所以我们需要做的工作由：安装oracle客户端、pdo_oci扩展、oci8扩展。

## 前期准备工作

1.对编译器进行更新：
```
$ sudo yum install php-pear php-devel zlib zlib-devel bc libaio glibc
$ sudo yum groupinstall "Development Tools"

yum install gcc-c++
yum install gcc-g77
```

2.下载oracle客户端

* 地址：http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html 
* 下载文件：
```
  （1）oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm
  （2）oracle-instantclient11.2-devel-11.2.0.4.0-1.x86_64.rpm
```

3.下载php的扩展文件（pdo_oci 和 oci8）

* Oracle的PDO接口：http://pecl.php.net/package/PDO_OCI  （PDO_OCI-1.0.tgz）
* php的Oracle扩展：http://pecl.php.net/package/oci8  （oci8-2.0.8.tgz）

## 安装 Oraclecient

1.安装rpm包：
```
rpm -ivh oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm
rpm -ivh oracle-instantclient11.2-devel-11.2.0.4.0-1.x86_64.rpm
```
一些安装前的检查：
```
rpm -qa | grep oracle     //查看oracle是否安装  
rpm -qa   //查看所有已安装的人rpm包  
rpm -e oracle-instantclient11.2-devel-11.2.0.4.0-1.x86_64.rpm //卸载已安装的rpm包  
rpm -ivh --force oracle-instantclient11.2-devel-11.2.0.4.0-1.x86_64.rpm //强制安装rpm包
```

2.安装完之后的配置

* 修改/etc/ld.so.conf  或在ld.so.conf.d文件夹下添加 oracle-x86_64.conf 文件,写入安装oracle客户端的lib路径:
```
#vi /etc/ld.so.conf  
/usr/lib/oracle/11.2/client64/lib/     //加入此行，保存退出
```

或者  
```
echo '/usr/lib/oracle/11.2/client64/lib/' > /etc/ld.so.conf.d/oracle-x86_64.conf
// 64位系统需要创建32位的软链接（这里可能是一个遗留bug，不然后面编译会出问题）
```

```
ln -s /usr/lib/oracle/11.2/client64 /usr/lib/oracle/11.2/client  
ln -s /usr/include/oracle/11.2/client64 /usr/include/oracle/11.2/client
```

3.定义环境变量
```
vi etc/profile
加入以下几行

export ORACLE_HOME=/usr/lib/oracle/11.2/client64/  
export LD_LIBRARY_PATH=/usr/lib/oracle/11.2/client64:$LD_LIBRARY_PATH  
export NLS_LANG="AMERICAN_AMERICA.AL32UTF8"
命令行输入以下语句使环境配置立即生效

#source /etc/profile
```

据说还可以这样，仅供参考：
```
Create a file inside /etc/profile.d named oracle.sh and put this as the content:

export LD_LIBRARY_PATH=/usr/lib/oracle/11.2/client64/lib
And run it so we’ll have LD_LIBRARY_PATH as an environment variable.

source /etc/profile.d/oracle.sh
```

## 安装oci8

提醒：编译的时候会出现错误：
```
error: oci8_dtrace_gen.h: No such file or directory
```
如果需要 DTRACE:
```
yum install systemtap-sdt-devel
export PHP_DTRACE=yes
```
如果不需要 DTRACE:
```
modify the file 'php_oci8_int.h', change the 48th line 

#include "oci8_dtrace_gen.h" to #undef HAVE_OCI8_DTRACE
```

* 一切就绪，编译安装
```
tar -xvf oci8-2.0.8.tgz  
cd oci8-2.0.8.tgz

/usr/bin/phpize
./configure --with-oci8=shared,instantclient,/usr/lib/oracle/11.2/client64/lib
make
make install
```
在/etc/php.d目录下增加配置文件20-oci8.ini
```
extension=oci8.so
```
用下面命令查看是否成功：
```
php -i | grep oci8
```
你可以看到下面类似的内容：
```
/etc/php.d/oci8.ini, oci8
oci8.connection_class => no value => no value
oci8.default_prefetch => 100 => 100 oci8.events => Off => Off oci8.max_persistent => -1 => -1 oci8.old_oci_close_semantics => Off => Off oci8.persistent_timeout => -1 => -1 oci8.ping_interval => 60 => 60 oci8.privileged_connect => Off => Off oci8.statement_cache_size => 20 => 20
```

## 安装PDO_OCI
