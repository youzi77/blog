
## 挂载分区
```
pvcreate /dev/vdb
vgdisplay
vgextend centos /dev/vdb
df -h
lvextend -L +500G /dev/mapper/centos-home
xfs_growfs /dev/mapper/centos-home
df -hl
```

## 安装jdk
```
vi /etc/profile


export JAVA_HOME=/home/admin/jdk1.8.0_271
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin

source /etc/profile
java -version
```