# docker安装数据库相关操作

## docker安装oracle11g

- 安装docker环境
- 安装oracle数据库

    1. 下载镜像

        ```linux
        docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
        ```
  
    2. 查看镜像

        ```linux
        docker images
        ```

    3. 创建容器

        ```linux
        docker run -d -p 1521:1521 --name oracle11g registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
        ```

        生成数执行脚本

        ```linux
        #!/bin/bash
        docker rm -f oracle11;
        docker run -it -d -p 1521:1521 -v /data/oracle:/data/oracle --name oracle11 registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
        ```

    4. 进入镜像当中进行相应配置

        - 使用命令进入docker的oracle镜像中

            ```linux
            docker exec -it oracle11 bash
            ```

        - 执行登录数据库

            ```linux
            sqlplus /nolog    
            ```

            若提示错误切换root用户然后执行下面命令
            1. ```su root``` 输入密码helowin
            2. 编辑profile文件配置ORACLE环境变量: ```vi /etc/profile```
                并在文件当中添加如下内容保存并退出

                ```linux
                export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
                export ORACLE_SID=helowin
                export PATH=$ORACLE_HOME/bin:$PATH
                ```

            3. 保存后执行 ```source /etc/profile``` 加载环境变量
            4. 创建软连接 ```ln -s $ORACLE_HOME/bin/sqlplus /usr/bin```
            5. 切换到oracle用户: ```su oracle```

        - 登录数据库并修改sys、system密码

            ```oracle
            sqlplus /nolog   --登录
            conn /as sysdba  --
            alter user system identified by system;--修改system用户账号密码；
            alter user sys identified by system;--修改sys用户账号密码；
            create user test identified by test; -- 创建内部管理员账号密码；
            grant connect,resource,dba to test; --将dba权限授权给内部管理员账号和密码；
            ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED; --修改密码规则策略为密码永不过期；（会出现坑，后面讲解）
            alter system set processes=1000 scope=spfile; --修改数据库最大连接数据；
            ```

        - 重启数据库

            ```oracle
            conn /as sysdba
            shutdown immediate; --关闭数据库
            startup; --启动数据库
            ```

        - 相关错误解释和解决方式

            1. 当执行修改密码的时候出现：`database not open`

                输入:`alter database open;`即可解决
                如果还有错误提示：`ORA-01507: database not mounted`
                那么依次输入:`alter database mount;`和`alter database open;`

            2. 暂无

    5. 连接数据库

        ``` java
        jdbc:oracle:thin:@xx.xx.xx.xx:1521:helowin
        ```

## docker安装mysql

- 查询mysql镜像

    ```linux
    docker search mysql
    ```

- 下载镜像
    不指定版本默认是最新稳定版本

    ```linux
    docker pull mysql
    ```

- 查看镜像:`docker images`
- 创建外挂配置文件

    ```linux
    cd /home/
    mkdir docker
    cd docker/
    mkdir mysql
    cd mysql
    echo $DOCKERMYSQL
    ```

- 启动mysql容器

    ```linux
    docker run --name mysql -v $DOCKERMYSQL/conf:/etc/mysql/conf.d -v $DOCKERMYSQL/logs:/logs -v $DOCKERMYSQL/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d -i -p 3306:3306 mysql:latest
    ```

- 查看mysql进程: `docker ps -a`
- 进入mysql容器，并登陆mysql

    ```linux
    docker exec -it mysqlserver bash
    mysql -uroot -p123456/mysql -uroot -p
    ```

    配置数据库开启远程登录权限：

    ```linux
    use mysql;
    select host,user from user;
    ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
    flush privileges;
    ```

- 数据库连接工具测试
