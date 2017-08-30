---
title: TCC柔性事务
date: 2017-08-30
categories: 架构
tags: [事务，架构]
---
>&emsp;&emsp;分布式事务是一个绕不过去的挑战！微服务架构本质上就是分布式服务化架构，微服务架构的流行，让分布式事务问题日益突出！尤其是在订单业务、资金业务等系统核心业务流程中，一定要有可靠的分布式事务解决方案来保证业务数据的可靠性和准确性。
## TCC事务机制简介
>&emsp;&emsp;关于TCC（Try-Confirm-Cancel）的概念，最早是由Pat Helland于2007年发表的一篇名为《Life beyond Distributed Transactions:an Apostate’s Opinion》的论文提出。在该论文中，TCC还是以Tentative-Confirmation-Cancellation作为名称；正式以Try-Confirm-Cancel作为名称的，可能是Atomikos（Gregor Hohpe所著书籍《Enterprise Integration Patterns》中收录了关于TCC的介绍，提到了Atomikos的Try-Confirm-Cancel，并认为二者是相似的概念）。

>&emsp;&emsp;国内最早关于TCC的报道，应该是InfoQ上对阿里程立博士的一篇采访。经过程博士的这一次传道之后，TCC在国内逐渐被大家广为了解并接受。相应的实现方案和开源框架也先后被发布出来。

>&emsp;&emsp;TCC事务机制相对于传统事务机制（X/Open XA），其特征在于它不依赖资源管理器(RM)对XA的支持，而是通过对（由业务系统提供的）业务逻辑的调度来实现分布式事务。对于业务系统中一个特定的业务逻辑S，其对外提供服务时，必须接受一些不确定性，即对业务逻辑执行的一次调用仅是一个临时性操作，调用它的消费方服务M保留了后续的取消权。如果M认为全局事务应该rollback，它会要求取消之前的临时性操作，这就对应S的一个取消操作。而当M认为全局事务应该commit时，它会放弃之前临时性操作的取消权，这对应S的一个确认操作。 每一个初步操作，最终都会被确认或取消。因此，针对一个具体的业务服务，TCC事务机制需要业务系统提供三段业务逻辑：初步操作Try、确认操作Confirm、取消操作Cancel。
***
<font color="red">1. 初步操作（Try）</font>
>&emsp;&emsp;TCC事务机制中的业务逻辑（Try），从执行阶段来看，与传统事务机制中业务逻辑相同。但从业务角度来看，是不一样的。TCC机制中的Try仅是一个初步操作，它和后续的次确认一起才能真正构成一个完整的业务逻辑。因此，可以认为[传统事务机制]的业务逻辑 = [TCC事务机制]的初步操作（Try） + [TCC事务机制]的确认逻辑（Confirm）。TCC机制将传统事务机制中的业务逻辑一分为二，拆分后保留的部分即为初步操作（Try）；而分离出的部分即为确认操作（Confirm），被延迟到事务提交阶段执行。
TCC事务机制以初步操作（Try）为中心，确认操作（Confirm）和取消操作（Cancel）都是围绕初步操作（Try）而展开。因此，Try阶段中的操作，其保障性是最好的，即使失败，仍然有取消操作（Cancel）可以将其不良影响进行回撤。
<font color="red">2. 确认操作（Confirm）</font>
>&emsp;&emsp;确认操作（Confirm）是对初步操作（Try）的一个补充。当TCC事务管理器认为全局事务可以正确提交时，就会逐个执行初步操作（Try）指定的确认操作（Confirm），将初步操作（Try）未完成的事项最终完成。
<font color="red">3. 取消操作（Cancel）</font>
>&emsp;&emsp;取消操作（Cancel）是对初步操作（Try）的一个回撤。当TCC事务管理器认为全局事务不能正确提交时，就会逐个执行初步操作（Try）指定的取消操作（Cancel），将初步操作（Try）已完成的事项全部撤回。

## 支付宝运营架构中柔性事务
> - <font color="red">柔性事务：业务活动</font>
>> ![柔性事务：业务活动](1.jpg)
- <font color="red">柔性事务：业务活动举例</font>
![柔性事务：业务活动举例](2.jpg)
- <font color="red">柔性事务：TCC型业务服务</font>
>> ![柔性事务：TCC型业务服务](3.jpg)
- <font color="red">柔性事务：TCC服务事务协调模式</font>
>> ![柔性事务：TCC服务事务协调模式](4.jpg)
>&emsp;&emsp;可以看出，柔性事务（[遵循BASE理论](http://localhost:4000/2017/08/25/cap/#BASE)）是指相对于[ACID刚性事务](http://localhost:4000/2017/07/08/transaction/#事务的-ACID)而言的。

>&emsp;&emsp;支付宝所说的柔性事务分为：两阶段型、补偿型、异步确保型、最大努力通知型几种。由于支付宝整个架构是SOA架构，因此传统单机环境下数据库的ACID事务满足了分布式环境下的业务需要，以上几种事务类似就是针对分布式环境下业务需要设定的。其中：
1、两阶段型：就是分布式事务两阶段提交，对应技术上的XA、JTA/JTS。这是分布式环境下事务处理的典型模式。
2、补偿型：TCC型事务（Try/Confirm/Cancel）可以归为补偿型。补偿型的例子，在一个长事务（long-running）中，一个由两台服务器一起参与的事务，服务器A发起事务，服务器B参与事务，B的事务需要人工参与，所以处理时间可能很长。如果按照ACID的原则，要保持事务的隔离性、一致性，服务器A中发起的事务中使用到的事务资源将会被锁定，不允许其他应用访问到事务过程中的中间结果，直到整个事务被提交或者回滚。这就造成事务A中的资源被长时间锁定，系统的可用性将不可接受。WS-BusinessActivity提供了一种基于补偿的long-running的事务处理模型。还是上面的例子，服务器A的事务如果执行顺利，那么事务A就先行提交，如果事务B也执行顺利，则事务B也提交，整个事务就算完成。但是如果事务B执行失败，事务B本身回滚，这时事务A已经被提交，所以需要执行一个补偿操作，将已经提交的事务A执行的操作作反操作，恢复到未执行前事务A的状态。这样的SAGA事务模型，是牺牲了一定的隔离性和一致性的，但是提高了long-running事务的可用性。例子来源：OASIS的WS-BusinessActivity文档
3、异步确保型将一些同步阻塞的事务操作变为异步的操作，避免对数据库事务的争用，典型例子是热点账户异步记账、批量记账的处理。
4、最大努力型PPT中提到的例子交易的消息通知（例如商户交易结果通知重试、补单重试）
## 参考
 [大规模SOA系统中的分布事务处事](https://wenku.baidu.com/view/be946bec0975f46527d3e104.html)
 [支付宝架构与技术](https://wenku.baidu.com/view/d1bbd25877232f60ddcca1d9.html)