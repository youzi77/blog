









## druid数据源介绍 阿里巴巴数据源常见问题解答

druid现在是目前做的最好,使用最为广泛的数据源,可能刚接触数据源会有一些些陌生,来看看数据源的阿里巴巴 druid 使用常见问题吧!
本文原地址:https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98
欢迎大家使用Druid，常见问题在这里解答，希望对大家有所帮助。

1. Druid是什么？
Druid是Java语言中最好的数据库连接池。Druid能够提供强大的监控和扩展功能。

2. 在哪里下载druid
正式版本下载：

maven中央仓库: http://central.maven.org/maven2/com/alibaba/druid/
3. 怎么获取Druid的源码
Druid是一个开源项目，源码托管在github上，源代码仓库地址是 https://github.com/alibaba/druid 。同时每次Druid发布正式版本和快照的时候，都会把源码打包，你可以从上面的下载地址中找到相关版本的源码

4. 怎么配置maven
Druid 0.1.18 之后版本都发布到maven中央仓库中，所以你只需要在项目的pom.xml中加上dependency就可以了。例如：

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>${druid-version}</version>
    </dependency>
也可以选择 Maven仓库查找公共的仓库地址: http://www.mvnrepository.com/artifact/com.alibaba/druid

5. 怎么打开Druid的监控统计功能
Druid的监控统计功能是通过filter-chain扩展实现，如果你要打开监控统计功能，配置StatFilter，具体看这里：https://github.com/alibaba/druid/wiki/配置_StatFilter

6. 怎样使用Druid的内置监控页面
内置监控页面是一个Servlet，具体配置看这里：
https://github.com/alibaba/druid/wiki/配置_StatViewServlet配置

7. 内置监控中的Web和Spring关联监控怎么配置？
Web关联监控配置

https://github.com/alibaba/druid/wiki/配置_配置WebStatFilter
Spring关联监控配置

https://github.com/alibaba/druid/wiki/配置_Druid和Spring关联监控配置
8. 怎么配置防御SQL注入攻击
Druid提供了WallFilter，它是基于SQL语义分析来实现防御SQL注入攻击的。具体配置看这里：
https://github.com/alibaba/druid/wiki/配置-wallfilter

9. Druid有没有参考配置
不同的业务场景需求不同，你可以使用我们的参考配置，但建议你仔细阅读相关文档，了解清楚之后做定制配置。
https://github.com/alibaba/druid/wiki/配置_DruidDataSource参考配置

10. 我想日志记录JDBC执行的SQL，如何配置
Druid提供了Log4jFilter、CommonsLogFilter和Slf4jFilter，具体配置看这里
https://github.com/alibaba/druid/wiki/配置_LogFilter

11. 我的程序可能产生连接泄漏了，有什么办法？
Druid提供了多种监测连接泄漏的手段，具体看这里：
https://github.com/alibaba/druid/wiki/连接泄漏监测

12. 在Druid中使用PSCache会有内存占用过大问题么？
连接Oracle数据库，打开PSCache，在其他的数据库连接池都会存在内存占用过多的问题，Druid是唯一解决这个问题的连接池。具体看这里：
https://github.com/alibaba/druid/wiki/Oracle数据库下PreparedStatementCache内存问题解决方案

13. 有没有和其他数据库连接池的对比？
各种数据库连接池对比
https://github.com/alibaba/druid/wiki/各种数据库连接池对比
14. 从其他连接池迁移要注意什么？
不同连接池的参数参照对比：
http://code.alibabatech.com/wiki/pages/viewpage.action?pageId=6947005
DBCP迁移 https://github.com/alibaba/druid/wiki/DBCP迁移
15. Druid中有没有类似Jboss DataSource中的ExceptionSorter
ExceptionSorter是JBoss DataSource中的优秀特性，Druid也有一样功能的ExceptionSorter，但不用手动配置，自动识别生效的。具体看这里：https://github.com/alibaba/druid/wiki/ExceptionSorter_cn

16. Druid中的maxIdle为什么是没用的？
maxIdle是Druid为了方便DBCP用户迁移而增加的，maxIdle是一个混乱的概念。连接池只应该有maxPoolSize和minPoolSize，druid只保留了maxActive和minIdle，分别相当于maxPoolSize和minPoolSize。

17. 我的应用配置的是JNDI数据源，可以用DruidDataSource么？
DruidDataSource支持JNDI配置，具体看这里：https://github.com/alibaba/druid/wiki/配置_JNDI_Tomcat

具体实现的类是这个：com.alibaba.druid.pool.DruidDataSourceFactory，你可以阅读代码加深理解。

18. 我的应用已使用DBCP，是代码中写死的，怎样更换为Druid？
可以的，Druid提供了一个中完全平滑迁移DBCP的办法。

从http://repo1.maven.org/maven2/com/alibaba/druid/druid-wrapper/ 下载druid-wrapper-xxx.jar
加入druid-xxx.jar
从你的WEB-INF/lib/中删除dbcp-xxx.jar
按需要加上配置，比如JVM启动参数加上-Ddruid.filters=stat，动态配置druid的filters
这种用法，使得可以在一些非自己开发的应用中使用Druid，例如在sonar中部署druid，sonar是一个使用jruby开发的web应用，写死了DBCP，只能够通过这种方法来更换。

19. 我想试用快照版本，怎么获取？
直接获取快照版本的地址是：http://code.alibabatech.com/mvn/snapshots/com/alibaba/druid/ ，使用快照版本建议加入我们QQ群 92748305，遇到问题直接反馈给我们。

20. 有一些SQL执行很慢，我希望日志记录下来，怎么设置？
在StatFilter配置中有慢SQL执行日志记录，看这里 https://github.com/alibaba/druid/wiki/配置_StatFilter

21. 我希望加密我的数据库密码怎么办？
运维和DBA都不希望把密码明文直接写在配置文件中，Druid提供了数据库密码加密的功能。具体看这里：
https://github.com/alibaba/druid/wiki/使用ConfigFilter

22. 如何参与Druid的开发
Druid是一个通过github开源的项目，github的特性，使得你很容易参与其中。这里有详细说明
https://github.com/alibaba/druid/wiki/如何参与

23. Druid的发布周期是怎样？
Druid是一个活跃的项目，长期维护。每个月有一个发布窗口，除非遇到重大bug和非常紧急的需求，否则都是每个月最多发布一次。如果没有足够多的需求，发布窗口就不会被使用。

24. 如果DruidDataSource在init的时候失败了，不再使用，是否需要close
是的，如果DruidDataSource不再使用，必须调用close来释放资源，释放的资源包括关闭Create和Destory线程。

25. DruidDataSource支持哪些数据库？
理论上说，支持所有有jdbc驱动的数据库。实际测试过的有

数据库	支持状态
mysql	支持，大规模使用
oracle	支持，大规模使用
sqlserver	支持
postgres	支持
db2	支持
h2	支持
derby	支持
sqlite	支持
sybase	支持
26. Oracle下jdbc executeBatch时，更新行数计算不正确
使用jdbc的executeBatch 方法，如果数据库为oracle，则无论是否成功更新到数据，返回值都是-2，而不是真正被sql更新到的记录数，这是Oracle JDBC Driver的问题，Druid不作特殊处理。

27. Druid如何自动根据URL自动识别DriverClass的
Druid是根据url前缀来识别DriverClass的，这样使得配置更方便简洁。

前缀	DriverCLass	描述信息
jdbc:odps	com.aliyun.odps.jdbc.OdpsDriver	
jdbc:derby	org.apache.derby.jdbc.EmbeddedDriver	
jdbc:mysql	com.mysql.jdbc.Driver	
jdbc:oracle	oracle.jdbc.driver.OracleDriver	
jdbc:microsoft	com.microsoft.jdbc.sqlserver.SQLServerDriver	
jdbc:sybase:Tds	com.sybase.jdbc2.jdbc.SybDriver	
jdbc:jtds	net.sourceforge.jtds.jdbc.Driver	
jdbc:postgresql	org.postgresql.Driver	
jdbc:fake	com.alibaba.druid.mock.MockDriver	
jdbc:mock	com.alibaba.druid.mock.MockDriver	
jdbc:hsqldb	org.hsqldb.jdbcDriver	
jdbc:db2	COM.ibm.db2.jdbc.app.DB2DriverD	B2的JDBC Driver十分混乱，这个匹配不一定对
jdbc:sqlite	org.sqlite.JDBC	
jdbc:ingres	com.ingres.jdbc.IngresDriver	
jdbc:h2	org.h2.Driver	
jdbc:mckoi	com.mckoi.JDBCDriver	
jdbc:cloudscape	COM.cloudscape.core.JDBCDriver	
jdbc:informix-sqli	com.informix.jdbc.IfxDriver	
jdbc:timesten	com.timesten.jdbc.TimesTenDriver	
jdbc:as400	com.ibm.as400.access.AS400JDBCDriver	
jdbc:sapdb	com.sap.dbtech.jdbc.DriverSapDB	
jdbc:JSQLConnect	com.jnetdirect.jsql.JSQLDriver	
jdbc:JTurbo	com.newatlanta.jturbo.driver.Driver	
jdbc:firebirdsql	org.firebirdsql.jdbc.FBDriver	
jdbc:interbase	interbase.interclient.Driver	
jdbc:pointbase	com.pointbase.jdbc.jdbcUniversalDriver	
jdbc:edbc	ca.edbc.jdbc.EdbcDriver	
jdbc:mimer:multi1	com.mimer.jdbc.Driver	
28. 如何保存监控记录
https://github.com/alibaba/druid/wiki/怎么保存Druid的监控记录

29. 我想Log输出SQL执行的信息怎么办？
https://github.com/alibaba/druid/wiki/配置_LogFilter

30. 如何配置Druid内置的log实现
https://github.com/alibaba/druid/wiki/配置druid内置的log实现

31. 如何在Spring Boot中集成Druid连接池和监控？
使用Druid Spring Boot Starter，文档地址：https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter

31. 如何在Spring Boot中添加自定义WallConfig、Filter ？
https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter#如何配置-filter

32. 如何在 Spring Boot 中配置数据库密码加密？
先看常见问题#21，如何生成加密后的密码及秘钥：我希望加密我的数据库密码怎么办？
进行配置
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
# 生成的加密后的密码（原密码 123456）
spring.datasource.password=WVMjPhfXQrIsWRo0/RCqAVvYtTU9WNVToKJohb8AlUmHwnV6vwFL+FM2CNFDMJwGHW1iCmyaUlF+sgvFdogqEA==
# 生成的公钥
public-key=MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAIiwHpFrDijV+GzwRTzWJk8D3j3jFfhsMFJ/7k1NTvBuLgL+TdIHgaMNOIEjHpXzuvX38J3FtOK8hLrySncVGOMCAwEAAQ==
# 配置 connection-properties，启用加密，配置公钥。
spring.datasource.druid.connection-properties=config.decrypt=true;config.decrypt.key=${public-key}
# 启用ConfigFilter
spring.datasource.druid.filter.config.enabled=true
33. 如何设置为让连接池知道数据库已经断开了，并且自动测试连接查询
加入以下配置：

<!-- 用来检测连接是否有效的sql，要求是一个查询语句，常用select 'x'。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会起作用。 -->
<property name="validationQuery" value="SELECT 1" />
<property name="testOnBorrow" value="false" />
<property name="testOnReturn" value="false" />
<property name="testWhileIdle" value="true" />
出错的异常信息摘要： Could not open JDBC Connection for transaction； nested exception is ...jdbc4... No operation allowed after connection closed;