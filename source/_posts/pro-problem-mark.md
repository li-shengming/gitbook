---
title: 生产故障Mark
layout: page
date: 2019-12-01
toc: true
categories: 
- 故障
tags: 
- async
- OOM
- 服务降级
---

## 前言
2019年是系统微服务化的第一年，在这一年中遇到了很多坎坷，也出现过几次重大故障。我把几次的典型的故障做个记录，避免后人犯相同的错误。
<!-- more-->
## 故障分析
### 故障一：慢SQL导致服务不可用
问题概述：新系统刚上线时，只试点了十几家营业部，数据量比较小，一切正常，当试点营业部增加到三百多家后，上午十点突然出现后端服务不可用
问题追溯：发现数据连接池达到最高线程数，大量sql执行速度较慢，尤其是计算开户列表count要执行10s+。数据库执行效率低，导致服务内部线程长期占用资源未释放，进而导致服务出现OOM情况。
解决方案：count查询暂时去掉，sql优化（区分大小权限）

### 故障二：外部服务响应缓慢导致渠道服务OOM
问题概述：外部服务正常情况下，都是1s以内返回结果，但是在系统异常情况下，长达60s都没有返回数据，进而导致服务内部线程长期占用资源，，进而导致服务出现OOM情况，最终导致服务不可用。
解决方案：增加熔断机制，做到服务降级。
> 这个问题出现频率较高，好多次，产品中心、规则服务、百行征信等

### 故障三：Async使用springboot默认配置导致服务出现OOM
问题描述：某一天服务内部突然出现OOM，服务内部线程数高达1.5w+
问题追溯：使用@Async之前一定要记得配置Async最小线程数，最大线程数，等待队列长度等配置，SpringBoot的默认配置是线程数不设上限，无限增加。
