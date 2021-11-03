### redis开启外网连接

- 找到redis的配置文件，注释掉bind 127.0.0.1这种，重启redis

- 停止redis：service redis stop

- 启动redis：service redis start

- 重启redis：service redis restart

- ```linux
  /etc/init.d/redis-server stop
  /etc/init.d/redis-server start
  /etc/init.d/redis-server restart
  ```

### redis安装

- 检查是否有redis yum 源 ：`yum install redis`
- 下载fedora的epel仓库: `yum install epel-release`
- 安装redis：`yum install redis`
- 启动redis：`service redis start`
- 停止redis：`service redis stop`
- 查看redis状态：`service redis status`
- 查看redis进程：`ps -ef | grep redis`
- 设置redis为开机自启动：`chkconfig redis on`

