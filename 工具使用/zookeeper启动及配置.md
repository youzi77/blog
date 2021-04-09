# 参考地址
https://www.cnblogs.com/rgever/p/10222167.html

返回主页
小毛喵喵
博客园首页新随笔联系管理订阅订阅随笔- 105  文章- 0  评论- 15 
linux中启动Zookeeper
1、先把zookeeper的安装包解压在/usr/local，如下：



2、进入zookeeper目录，创建一个data目录



3、进入同级conf目录下，里面有zoo_sample.cfg,修改该文件名称为zoo.cfg



4、进入该文件zoo.cfg，修改下面字段的值：

①进入



②修改前：



③修改后：（修改后的目录就是刚才新建的目录位置）



保存退出即可

5、启动zookeeper



6、查看日志zookeeper.out文件



原因是java的环境配置错了，此处可以参考之前的博客：linux上安装jdk       https://www.cnblogs.com/rgever/p/9484851.html

7、修改之后再次启动zookeeper：



成功！！！！

 --------------------------------------------------------------------------分割线-------------------------------------------------------------------------------------

还有可能造成上述错误的原因是：在上面新建的目录data中有一个.pid文件，删掉该pid文件重新启动即可运行了



## 启动
`./zkServer.sh start`
## 查看状态
`./zkServer.sh status`
`./zkServer.sh stop`