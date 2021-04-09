//1 SSL功能需要openssl库，直接通过yum安装: 
yum install openssl
//2 gzip模块需要zlib库，直接通过yum安装: 
yum install zlib
//3 rewrite模块需要pcre库，直接通过yum安装: 
yum install pcre

## 安装依赖
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

## 安装nginx
yum install nginx

