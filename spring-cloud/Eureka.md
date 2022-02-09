## Eureka介绍

**Eureka**是**SpringCloud**微服务解决方案框架中的一个组件Eureka注册中心， 由[Netflix](https://github.com/Netflix) 开发的，一个基于 REST 服务的，服务注册与发现的组件。

包含两个组件：

- **Eureka Client：**一个Java客户端，用于简化与 Eureka Server 的交互（通常就是微服务中的客户端和服务端）

 - **Eureka Server：**提供服务注册和发现的能力（通常就是微服务中的注册中心）

## Eureka特点

在各个微服务启动时，会通过 Eureka Client 向 Eureka Server 注册自己，Eureka Server 会存储该服务的信息

- **同步：**每个 Eureka Server 同时也是 Eureka Client（逻辑上的）
  　　　多个 Eureka Server 之间通过复制的方式完成服务注册表的同步，形成 Eureka 的高可用

- **识别：**Eureka Client 会缓存 Eureka Server 中的信息
  　　　即使所有 Eureka Server 节点都宕掉，服务消费者仍可使用缓存中的信息找到服务提供者
- **续约：**微服务会周期性（默认30s）地向 Eureka Server 发送心跳以Renew（续约）信息（类似于heartbeat）

- **续期：**Eureka Server 会定期（默认60s）执行一次失效服务检测功能
  　　　它会检查超过一定时间（默认90s）没有Renew的微服务，发现则会注销该微服务节点，这个称作Eureka保护机制