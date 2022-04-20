### 挂载分区

```
pvcreate /dev/vdb
vgdisplay
vgextend centos /dev/vdb
df -h
lvextend -L +500G /dev/mapper/centos-home
xfs_growfs /dev/mapper/centos-home
df -hl
```

### 安装jdk

```
vi /etc/profile


export JAVA_HOME=/home/admin/jdk1.8.0_271
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin

source /etc/profile
java -version
```

### 查询端口占用

`lsof -i tcp:8080`

`netstat -anp |grep `

### shh连接

- 普通密码连接

  ```
  ssh -p 端口号 用户@地址
  ```

- 证书连接

  ```
  ssh -i 证书地址 用户@地址
  ```

### 查看磁盘大小

```linux
Df -h
```

### scp复制文件

- 普通账号密码

  ```linux
  scp /home/space/music/1.mp3 root@www.runoob.com:/home/root/others/music 
  ```

- 使用秘钥

  ```
  scp -i 秘钥 上传文件 用户@地址:上传路径
  ```

- 下载文件

  ````
  scp username@servername:/path/filename /tmp/local_destination
  ````

### zip的压缩和解压

- 压缩文件

  ``` 
  zip -r xxx.zip ./*
  ```

- 解压文件到当前目录

  ```
  unzip xxxx.zip
  ```

- 带参数解压

  ```
  unzip xxxx.zip -o -d /home/data/
  -o:不提示的情况下覆盖文件
  -d: /home/data 指明将文件解压缩到 /home/data 目录下
  ```

- 删除压缩文件当中的某个文件

  ```
  zip -d xx.zip xx.txt
  ```

- 向压缩文件当中添加某个文件

  ```
  zip -r xxx.zip ./xxx.txt
  ```

- tar 命令详解

  　　-c: 建立压缩档案

    　　-x：解压

    　　-t：查看内容

    　　-r：向压缩归档文件末尾追加文件

    　　-u：更新原压缩包中的文件

    　　这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

    　　-c: 建立压缩档案

    　　-x：解压

    　　-t：查看内容

    　　-r：向压缩归档文件末尾追加文件

    　　-u：更新原压缩包中的文件

    　　下面的参数-f是必须的

    　　-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

### jar的解压和重新压缩

```linux
jar cvfM0 springcloud-zuul.jar *                 // 压缩当前目前所有文件到.jar
jar cvfM0 spring-zuul.jar E:/springcloud-zuul/   // 指定压缩目录
jar xvf test.jar   //解压缩
unzip xxx.war -d /home/aaa ：
```

### 查找进程
```
ps -aux | grep nginx
```

### 加入开机启动

```
systemctl start nginx
```