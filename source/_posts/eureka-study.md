---
title: Eureka Study
layout: post
categories: SpringCloud
tags: Eureka SpringCloud SourceCode
excerpt: Eureka调研
---

### Eureka介绍

Eureka是Spring Cloud Netflix微服务套件中的一部分，可以与Springboot构建的微服务很容易的整合起来。 
Eureka包含了服务器端和客户端组件。
- 服务器端，也被称作是服务注册中心，用于提供服务的注册与发现。Eureka支持高可用的配置，当集群中有分片出现故障时，Eureka就会转入自动保护模式，它允许分片故障期间继续提供服务的发现和注册，当故障分片恢复正常时，集群中其他分片会把他们的状态再次同步回来。
- 客户端组件包含服务消费者与服务生产者。在应用程序运行时，Eureka客户端并把他们缓存到本地并周期性的刷新服务状态。

### Eureka原理

![Eureka原理](https://li-shengming.github.io/pictures/eureka/eureka-operate.png)

- 第一步，spider会启动一个新的docker容器，并在新容器上部署被启动服务的镜像，然后启动服务；
    - 如果采用的是SpringCloud框架，新起的服务会马上把信息注册到EUREKA上；
    - 如果是非SpringCloud，周期性（默认30s，可通过eureka.client.instance-info-replication-interval-seconds调整）的将信息注册到EUREKA上；
- 第二步，EUREKA收到客户端上报的注册信息后，将新注册服务信息放到readWriteCacheMap中，同时周期性（默认30s，可通过eureka.server.response-cache-update-interval-ms调整）的将信息刷新到缓存中（readOnlyCacheMap）中
    - 客户端拉取信息是从缓存（readOnlyCacheMap）中拉取的【readOnlyCacheMap这个可通过eureka.server.use-read-only-response-cache=false关闭的，由于目前EUREKA是公用的，基础研发部反馈，默认配置是不能动的】；
    - 如果你通过web页面去查看eureka上客户端的注册信息，看到的数据是readWriteCacheMap中的，也就是说，你即便在页面上看到了新启动服务的信息，也不代表调用方已经获取到了最新的实例信息；
- 第三步，客户端会定期（默认30s,可通过eureka.client.registry-fetch-interval-seconds调整）从EUREKA上拉取最新的注册信息
- 第四步，如果客户端采用了ribbon进行负载均衡，ribbon使用ribbon缓存进行负载均衡，客户端会定期（默认30s,可通过ribbon.ServerListRefreshInterval进行调整）最新拉取到的信息同步到ribbon缓存

理解了以上四步，我们思考一个问题，新服务上线，客户端最大可能多久可以拿到最新的服务信息

- SpringCloud下=0(首次注册 init registe) + 30(readOnlyCacheMap)+30(client fetch interval)+30(ribbon)=90
- 非SpringCloud下=30(首次注册 init registe) + 30(readOnlyCacheMap)+30(client fetch interval)+30(ribbon)=120

结论：90s的延迟，完全可能存在这样的场景：新服务已经起来了，老服务已经关闭，但是客户端由于没有拿到最新的地址信息，导致服务出现中断问题。

上面只分析了服务上线的情况，还有服务下线的情况，如果没有做特殊配置的话，EUREKA连续3个心跳周期没有检测到客户端心跳的话，会将这一节点剔除，客户端获取到服务不可用的信息会更晚。

    网上查到的资料提醒：现在eureka自动下线存在BUG,自动剔除时间会翻倍，需要6个周期，最长要270s

