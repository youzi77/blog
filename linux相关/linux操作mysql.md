- 查询mysql版本号
  1. 在终端中查看:`mysql -v`
  2. 在help中查看:`mysql --help |grep Distrib`
  3. 在mysql中查看:`select version()`
  4. 在mysql中查看:`status`

- ~~安装mysql~~
  - 下载安装包 ` wget https://cdn.mysql.com/archives/mysql-5.7/mysql-5.7.30-linux-glibc2.12-x86_64.tar.gz`
  - 解压:`tar -xvzf xxxxx.tar.gz`
  - 移动:`mv mysql-5.7.30-linux-glibc2.12-x86_64 /usr/local/mysql`
  - 创建data:`mkdir data`
  - 更改目录权限:`chown -R mysql:mysql /var/lib/mysql;chown -R mysql:mysql /usr/local/mysql    ;chmod -R 755 /usr/local/mysql`
  - 进入安装目录:`cd /usr/local/mysql/bin`
  - 安装:`./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql`
  - 记住临时密码:` [Note] A temporary password is generated for root@localhost: 5xrO_BXXkkpk`
  - 编辑配置文件my.cnf :`vi /etc/my.cnf`
  - 基础配置:`datadir=/usr/local/mysql/data
    port = 3306
    sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
    symbolic-links=0
    max_connections=400
    innodb_file_per_table=1
    lower_case_table_names=1
    character_set_server=utf8`
  - 启动mysql服务器:`/usr/local/mysql/support-files/mysql.server start`



- 安装mysql

  1. 查看系统中是否已安装了相关的mysql产品,如果有但不需要的将其卸载

     `rpm -qa | grep -i mysql`
     卸载

     ` rpm -e --nodeps qt-mysql-4.8.5-8.el7.x86_64`

     ` rpm -e --nodeps perl-DBD-MySQL-4.023-5.el7.x86_64`

     ` rpm -e --nodeps akonadi-mysql-1.9.2-4.el7.x86_64`

  2. 版本：mysql-5.7.20-1.el6.x86_64  环境:linux7.x
     官方下载地址:

     `wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.20-1.el6.x86_64.rpm-bundle.tar`

     解压:

     `tar -xvf mysql-5.7.20-1.el6.x86_64.rpm-bundle.tar`

     卸载之前的版本

     `rpm -qa | grep -i mysql`

     `rpm -e xxxxx [之前安装的版本] --nodeps 要卸载的mysql版本`

     按照步骤来安装就好

     `rpm -ivh mysql-community-libs-5.7.20-1.el6.x86_64.rpm --nodeps --force`

     `rpm -ivh mysql-community-devel-5.7.20-1.el6.x86_64.rpm --nodeps --force`

     `rpm -ivh mysql-community-client-5.7.20-1.el6.x86_64.rpm  --nodeps --force`

     `rpm -ivh mysql-community-server-5.7.20-1.el6.x86_64.rpm  --nodeps --force`

     修改密码 （有点特殊 ）

     ```
         1.找到my.cnf |  本机  /etc/my.cnf
     
         2.[mysqld]#下添加
     
           skip-grant-tables=1 #不用验证
     
         3.开启mysql /etc/init.d/mysqld start|本机默认地址
     
         4.进入并修改（依次命令）
     
             1. mysql -uroot -p;
     
             2.use mysql
     
             3.update user set authentication_string = password('123456'), password_expired = 'N', password_last_changed = now() where user = 'root';
     
          5.my.cnf |  本机  /etc/my.cnf 里面删除skip-grant-tables=1 
     
     6.测试
     
         mysql -uroot -p123456 -e "show databases;";
     
     7.OK收工;
     ```

  