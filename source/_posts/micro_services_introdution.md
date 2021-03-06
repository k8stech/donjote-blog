---
title: 微服务简介
date: 2017-07-04
categories: 架构
tags: [微服务,架构]
---
## Monolithic架构
![Monolithic架构](1.png)
Monolithic比较适合小项目
### Monolithic架构优点
- 开发简单直接，集中式管理, 基本不会重复开发
- 功能都在本地，没有分布式的管理开销和调用开销。
### Monolithic架构缺点
它的缺点也非常明显，特别对于互联网公司来说（不一一列举了）：
- 开发效率低：所有的开发在一个项目改代码，递交代码相互等待，代码冲突不断
- 代码维护难：代码功能耦合在一起，新人不知道何从下手
- 部署不灵活：构建时间长，任何小修改必须重新构建整个项目，这个过程往往很长
- 稳定性不高：一个微不足道的小问题，可以导致整个应用挂掉
- 扩展性不够：无法满足高并发情况下的业务需求

## 微服务架构
### 什么是微服务
相对于单体（Monolithic）应用而言，微服务是采用一组服务的方式来构建一个应用，服务独立部署在不同的进程中，不同服务通过一些轻量级交互机制来通信，例如 RPC、HTTP 等,服务可独立扩展伸缩，每个服务定义了明确的边界，不同的服务甚至可以采用不同的编程语言来实现，由独立的团队来维护。

### 微服务的价值
- 首先，在功能不变的情况下，应用被分解为多个可管理的服务，每个服务开发、运维变得简单。
- 其次，每个微服务独立部署，开发者不在需要协调其他服务部署对本服务的影响，这可以加快部署精度。
- 最后，每个服务可以自行决定自己的容量。
***
虽然对于一些简单的、规模有限的应用而言，单体架构仍然是有意义的。微服务是应用开发和部署的一个不同的方法。它非常适合许多现代的云应用对于敏捷性、扩展规模和可靠性的要求。一个微服务应用被分解成独立的部件，被称为“微服务”。“微服务”协同工作，以便提供应用的整体功能。“微服务”这一术语强调一个事实，那就是应用应该是由足够小的服务所组成，以便真正体现独立性，使得每个微服务实现单一的功能。此外，每个微服务都有明确的合同（API合同）——通常是RESTful 的——以便其他微服务能够与之进行交流和分享数据。微服务也必须能够彼此独立地进行版本更新。这种松耦合正是对一个应用实现快速而可靠地演化的支撑。下图显示了一个单体应用是如何被分解为不同的微服务的。
![微服务架构](2.png)
### 微服务的特点
- 小, 且专注于做⼀件事情
- 进程独立
- 轻量级的通信机制
- 松耦合
- 独立部署
***
**领域驱动设计：** 应用程序功能分解可以通过Eric Evans在[《领域驱动设计》](http://www.jdon.com/ddd.html)中明确定义的规则实现；每个团队负责与一个领域或业务功能相关的全部开发；团队拥有全系列的开发人员，具备用户界面、业务逻辑和持久化存储等方面的开发技能；
**单一职责原则：** 每个服务应该负责该功能的一个单独的部分，这是[面向对象设计的SOLID原则](http://www.cnblogs.com/shanyou/archive/2009/09/21/1570716.html)原则之一；
**明确发布接口：** 每个服务都会发布一个定义明确的接口，而且保持不变；服务消费者只关心接口，而对于被消费的服务没有任何运行依赖；
**独立部署、升级、扩展和替换：** 每个服务都可以单独部署及重新部署而不影响整个系统。这使得服务很容易升级，每个服务都可以沿着[《架构即未来》](https://pan.baidu.com/s/1qYpzBPE)一书定义的[AKF扩展立方体](http://www.jianshu.com/p/d08d0c14810f)的X轴和Z轴进行扩展；
**可以异构/采用多种语言：**　每个服务的实现细节都与其它服务无关，这使得服务之间能够解耦，团队可以针对每个服务选择最合适的开发语言、持久化存储、工具和方法；
**轻量级通信：**　服务通信使用轻量级的通信协议，例如，同步的REST，异步的AMQP、STOMP、MQTT等。
***
微服务架构的思想本质跟互联网的思想是一致的。它的组件对外发布的服务视同HTTP协议，采用HTTP Rest API的方式来进行。很多开放平台的API服务，基本都采用了Http API的方式进行服务的发布和管理。

### 微服务优点
- 每个微服务都很小，这样能聚焦一个指定的业务功能或业务需求。
- 微服务能够被小团队单独开发，这个小团队是2到5人的开发人员组成。
- 微服务是松耦合的，是有功能意义的服务，无论是在开发阶段或部署阶段都是独立的。
- 微服务能使用不同的语言开发。
- 微服务允许容易且灵活的方式集成自动部署，通过持续集成工具，如Jenkins, bamboo 。
- 一个团队的新成员能够更快投入生产。
- 微服务易于被一个开发人员理解，修改和维护，这样小团队能够更关注自己的工作成果。无需通过合作才能体现价值。
- 微服务允许你利用融合最新技术。
- 微服务只是业务逻辑的代码，不会和HTML,CSS 或其他界面组件混合。
- 微服务能够即时被要求扩展。
- 微服务能部署中低端配置的服务器上。
- 易于和第三方集成。
- 每个微服务都有自己的存储能力，可以有自己的数据库。也可以有统一数据库。

### 微服务架构的缺点
- 微服务架构可能带来过多的操作。
- 需要DevOps技巧 (http://en.wikipedia.org/wiki/DevOps).
- 可能双倍的努力。
- 分布式系统可能复杂难以管理。
- 因为分布部署跟踪问题难。
- 服务数量增加，管理复杂性增加。

### 常见的微服务组件
- 服务注册:服务提供方将自己调用地址注册到服务注册中心，让服务调用方能够方便地找到自己。
- 服务发现:服务调用方从服务注册中心找到自己需要调用的服务的地址。
- 负载均衡:服务提供方一般以多实例的形式提供服务，负载均衡功能能够让服务调用方连接到合适的服务节点。并且，节点选择的工作对服务调用方来说是透明的。
- 服务网关:服务网关是服务调用的唯一入口，可以在这个组件是实现用户鉴权、动态路由、灰度发布、A/B测试、负载限流等功能。
- 配置中心:将本地化的配置信息（properties,
xml, yaml等）注册到配置中心，实现程序包在开发、测试、生产环境的无差别性，方便程序包的迁移。
- API管理:以方便的形式编写及更新API文档，并以方便的形式供调用者查看和测试。
- 集成框架:微服务组件都以职责单一的程序包对外提供服务，集成框架以配置的形式将所有微服务组件（特别是管理端组件）集成到统一的界面框架下，让用户能够在统一的界面中使用系统。
- 分布式事务:对于重要的业务，需要通过分布式事务技术（TCC、高可用消息服务、最大努力通知）保证数据的一致性。具有代表性的有spring transaction
- 调用链:记录完成一个业务逻辑时调用到的微服务，并将这种串行或并行的调用关系展示出来。在系统出错时，可以方便地找到出错点。具有代表性的有pinpoint.
- 支撑平台:系统微服务化后，系统变得更加碎片化，系统的部署、运维、监控等都比单体架构更加复杂，那么，就需要将大部分的工作自动化。现在，可以通过Docker等工具来中和这些微服务架构带来的弊端。 例如:持续集成、蓝绿发布、健康检查、性能健康等等。严重点，以我们两年的实践经验，可以这么说，如果没有合适的支撑平台或工具，就不要使用微服务架构。
