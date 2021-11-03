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

  

