## PHP7配置安装扩展的一般流程
```
1.下载php7源码，进入相应扩展目录，一般是：php-7.x.x/ext/xxx/

2.执行：/usr/local/php7/bin/phpize 

3.初始化相应扩展（此命令仅供参照）：./configure --enable-exif --with-php-config=/usr/local/php7/bin/php-config

4.执行编译安装：make && sudo make install

5.确认扩展是否已经安装好，可以去到扩展目录：/usr/local/php7/lib/php/extensions/no-debug-non-zts-20151012

6.确认扩展已经安装好了，就可以在php.ini开启扩展，找到文件(一般存放路径)：/usr/local/php7/lib/php.ini

7.加上配置：extension=xxx.so

8.保存后，重启php-fpm，即可。
```

* 疑惑：扩展的后缀也很有意思，windows下的php扩展文件后缀是.dll，而linux下的php扩展文件后最是.so


## 安装扩展 exif

```
1.下载PHP源码，进入到源码扩展目录：php-7.x.x/ext/exif/

2.执行：/usr/local/php7/bin/phpize 

3.初始化相应扩展（此命令仅供参照）：./configure --enable-exif --with-php-config=/usr/local/php7/bin/php-config

4.执行编译安装：make && sudo make install

5.确认扩展安装完成后，修改配置文件php.ini，在文件内容的最后加上：extension=exif.so

6.保存php.ini后，重启php-fpm
```

## 安装扩展 oci8

```
1.下载相对应PHP7的扩展源码，比如 PHP7.0.3 可以装 oci2.1.0

2.进入扩展目录：~/oci8-2.1.0/

3.初始化相应扩展（此命令仅供参考）：./configure --with-oci8=shared,instantclient,/usr/lib/oracle/11.2/client64/lib --with-php-config=/usr/local/php7/bin/php-config

4.执行编译安装：make && sudo make install

5.确认扩展安装完成后，修改配置文件php.ini，在文件内容的最后加上：extension=oci8.so

6.保存php.ini后，重启php-fpm
```

## 安装扩展 pdo_oci （大坑特坑）

```
1.下载PHP源码，进入到源码扩展目录：php-7.x.x/ext/pdo_oci/

2.执行：/usr/local/php7/bin/phpize 

3.初始化相应扩展（此命令仅供参考）： ./configure --with-pdo-oci=instantclient,/usr,10.2.0.2 --with-php-config=/usr/local/php7/bin/php-config

4.执行编译安装：make && sudo make install

5.确认扩展安装完成后，修改配置文件php.ini，在文件内容的最后加上：extension=pdo_oci.so

6.保存php.ini后，重启php-fpm
```

 ### 「PHP7安装pdo_oci的坑」
 
 1. 首先你需要确认你安装的 oracle 版本是不是适应 PHP7，很多时候PHP7最好安装 11.2 版本的oracle，其他或许也可以，欢迎指正。

 2. 在编译安装 pdo_oci 的时候，需要给 oracle11.2 建一个软连接(做法仅供参考)：
 ```
  sudo ln -s /usr/include/oracle/11.2 /usr/include/oracle/10.2.0.2
  sudo ln -s /usr/include/oracle/11.2 /usr/include/oracle/10.2.0.2
 ```
 
 3. 注意编译安装时出现的问题，必要的时候，可能需要重新安装 pdo_oci，但前提需要记住，一定要执行 make clean。

