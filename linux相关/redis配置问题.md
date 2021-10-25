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

  