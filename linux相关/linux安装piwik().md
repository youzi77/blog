# piwik(Matoma)的安装

Piwik 是一套基于 Php+MySQL 技术构建，能够与 Google Analytics 相媲美的开源网站访问统计系统。Piwik 可以给你详细的统计信息，比如网页浏览人数, 访问最多的页面, 搜索引擎关键词等等，并且采用了大量的AJAX/Flash技术，使得在操作上更加便易。

现在piwik最新版本已经更名为Matoma
## 1、php的安装
- **说明** 
    
    这里我使用的安装方式是yum命令进行安装
- **卸载**
    
    首先将系统原来的php卸载删除掉
    
    ```yum -y remove php* ```
- **设置安装源**
    
    - 方法1

       ```rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm```

       ```rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm```
       
       **说明:** 设置php安装的镜像源
     
    - 方法2
        
        ```yum installepel-release -yrpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm ```
- **安装php不同版本**
   
     **说明:** 关于安装什么版本的piwik这取决于你安装了什么piwik根据piwik去选择要安装的php版本,这里我使用的是php72w，关于php不同版本之前的区别和差异，需要各位看官自行查看官方文档了
     - php基本使用

        ```yum -y install php72w php72w-cli php72w-fpm php72w-common php72w-devel```
     - php piwik所需模块

        ```yum -y install php72w php72w-cli php72w-fpm php72w-common php72w-devel php72w-embedded php72w-gd php72w-mbstring php72w-mysqlnd php72w-opcache php72w-pdo php72w-xml```

- **php服务启动相关命令**
    
    **说明:** 主要记录php服务的启动停止和重启命令
    - 添加到开启启动项 ```systemctl enablephp-fpm.service```
    - 开启php服务 ```systemctl start php-fpm.service```
    - 停止php服务 ```systemctl stop php-fpm.service```
    - 重启php服务 ```systemctl restart php-fpm.service```

## 2. mysql的安装
    
略
    


    
