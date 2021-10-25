##  前置安装
  - 安装cmake yum install cmake
  - 安装gcc
      ```
      yum -y install gcc  
      yum -y install gcc-c++ 
      ```


## linun 安装Anaconda3
```
bash ./Anaconda3-2018.12-Linux-x86_64.sh
sudo vi /etc/profile
添加启动地址
#Anaconda
export PATH=$PATH:/home/software/anaconda3/bin
source /etc/profile
#安装python3.5
conda create -n py3.5_boat_plate python==3.5
```


## linux 安装opencv

```
cd  opencv-3.4.12
mkdir release
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..
make
sudo make install
```


# linux 安装apt-get
```
curl https://raw.githubusercontent.com/dvershinin/apt-get-centos/master/apt-get.sh -o /usr/local/bin/apt-get
chmod 0755 /usr/local/bin/apt-get
#添加环境变量或者
#添加环境变量
    修改/etc/profile
    export PATH=$PATH:/usr/local/webserver/php/bin:/usr/local/webserver/mysql/bin
    执行 命令source /etc/profile 生效

/usr/local/bin/apt-get  install 包名


```
## linux 安装出现 没有可用软件包
在Linux系统中使用yum安装软件时提示”没有可用软件包“时，代表在linux系统yum源中已经没有对应的安装包了，这时，我们需要安装EPEL。

EPEL（Extra Packages for Enterprise Linux）企业版Linux额外包，PHEL分布非标准包的社区类库。安装如下：

`yum install -y epel-release `

## Centos7安装opencv-python缺少共享库
- 安装libSM.so.6

- 我们直接通过yum来安装libSM解决不了问题, 那是因为yum源默认提供的库是i686的, 如果我们的服务器系统是64位的,应该要安装的是x86_64版而非i686.


    `yum whatprovides libSM.so.6`
    
    [../blog/images/linux.png]
    
    `sudo yum install libSM-1.2.2-2.el7.x86_64 --setopt=protected_multilib=false`

- python 调用opencv
  错误：libmvec.so.1 
  解决方式：
  ```
  yum install gcc gcc-c++
  wget http://ftp.gnu.org/gnu/glibc/glibc-2.25.tar.gz
  tar xvzf glibc-2.25.tar.gz
  cd glibc-2.25
  mkdir build
  cd build
  ../configure --prefix=/usr
  make -i
  make install -i
  ```
- libgomp.so.1 错误
  ```
  yum install libgomp.i686
  ```
- libgomp.so.1 错误
    ```
    strings /root/anaconda3/bin/../lib/libgomp.so.1 | grep GOMP
    
    cp /usr/lib/libgomp.so.1.0.0 /root/anaconda3/lib
    
    rm -f libgomp.so.1
    
    ln -s libgomp.so.1.0.0 libgomp.so.1
    ```

