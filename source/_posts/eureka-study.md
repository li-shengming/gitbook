---
title: Eureka Study
layout: post
categories: SpringCloud
tags: Eureka SpringCloud SourceCode
excerpt: Eureka调研
---

## Eureka介绍
Eureka是Spring Cloud Netflix微服务套件中的一部分，可以与Springboot构建的微服务很容易的整合起来。 
Eureka包含了服务器端和客户端组件。
- 服务器端，也被称作是服务注册中心，用于提供服务的注册与发现。Eureka支持高可用的配置，当集群中有分片出现故障时，Eureka就会转入自动保护模式，它允许分片故障期间继续提供服务的发现和注册，当故障分片恢复正常时，集群中其他分片会把他们的状态再次同步回来。
- 客户端组件包含服务消费者与服务生产者。在应用程序运行时，Eureka客户端并把他们缓存到本地并周期性的刷新服务状态。

## Eureka原理
