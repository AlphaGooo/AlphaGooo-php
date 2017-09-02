## PHP7配置安装扩展的一般流程
```
1.下载php7源码，进入相应扩展目录，一般是：php-7.x.x/ext/xxx/

2.执行：/usr/local/php7/bin/phpize 

3.安装相应扩展（此命令仅供参照）：./configure --enable-exif --with-php-config=/usr/local/php7/bin/php-config

4.执行编译：make && sudo make install

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

3.安装相应扩展（此命令仅供参照）：./configure --enable-exif --with-php-config=/usr/local/php7/bin/php-config

4.执行编译：make && sudo make install

5.确认扩展安装完成后，修改配置文件php.ini，在文件内容的最后加上：extension=exif.so

6.保存php.ini后，重启php-fpm
```
