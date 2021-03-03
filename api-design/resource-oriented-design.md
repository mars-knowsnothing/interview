# 基于资源的设计
## 原则
### 将 RPC API（基于套接字）与 REST API（基于 HTTP）的设计融合起来    
    RPC API 通常根据接口和方法设计。随着时间的推移，接口和方法越来越多，最终结果可能是形成一个庞大而混乱的 API 接口，因为开发者必须单独学习每种方法。显然，这既耗时又容易出错。

### 核心原则是定义可以用少量方法控制的命名资源
    资源和方法被称为 API 的“名词”和“动词”。使用 HTTP 协议时，资源名称自然映射到网址，方法自然映射到 HTTP 的 POST、GET、PUT、PATCH 和 DELETE。这使得要学习的内容减少了很多，因为开发人员可以专注于资源及其关系，并假定它们拥有的标准方法同样很少。

* 虽然 HTTP REST API 在互联网上非常流行，但它们承载的流量比传统的 RPC API 要小。出于性能考虑，很少有人会使用 REST API 来传送互联网流量是视频内容

### API 平台应该为所有类型的 API 提供最佳支持
    在实际使用中，人们会出于不同目的选择 RPC API 和 HTTP REST API，Google API设计指南将面向资源的设计原则应用于通用 API 设计并定义了许多常见的设计模式，从而提高可用性并降低复杂性。

## 什么是 REST API
    REST API 是可单独寻址的“资源”（API 中的“名词”）的“集合”。资源通过资源名称被引用，并通过一组“方法”（也称为“动词”或“操作”）进行控制。

    REST Google API 的标准方法（也称为“REST 方法”）包括 List、Get、Create、Update 和 Delete。API 设计者还可以使用“自定义方法”（也称为“自定义动词”或“自定义操作”）来实现无法轻易映射到标准方法的功能（例如数据库事务）。

* 自定义动词并不意味着创建自定义 HTTP 动词来支持自定义方法。对基于 HTTP 的 API 而言，它们只是映射到最合适的 HTTP 动词。

## 设计流程

1. 确定 API 提供的资源类型。
2. 确定资源之间的关系。
3. 根据类型和关系确定资源名称方案。
4. 确定资源架构。
5. 将最小的方法集附加到资源。
### 资源
    面向资源的 API 通常被构建为资源层次结构，其中每个节点是一个“简单资源”或“集合资源”。 为方便起见，它们通常被分别称为资源和集合。

    一个集合包含相同类型的资源列表。 例如，一个用户拥有一组联系人。

    资源具有一些状态和零个或多个子资源。 每个子资源可以是一个简单资源或一个集合资源。

    例如，Gmail API 有一组用户，每个用户都有一组消息、一组线程、一组标签、一个个人资料资源和若干设置资源。

    虽然存储系统和 REST API 之间存在一些概念上的对应，但具有面向资源 API 的服务不一定是数据库，并且在解释资源和方法方面具有极大的灵活性。例如，创建日历事件（资源）可以为参与者创建附加事件、向参与者发送电子邮件邀请、预约会议室以及更新视频会议时间安排。

### 方法
    面向资源的 API 的关键特性是，强调资源（数据模型）甚于资源上执行的方法（功能）。典型的面向资源的 API 使用少量方法公开大量资源。方法可以是标准方法或自定义方法。对于本指南，标准方法有：List、Get、Create、Update 和 Delete。

    如果 API 功能能够自然映射到标准方法，则应该在 API 设计中使用该方法。对于不会自然映射到某一标准方法的功能，可以使用自定义方法。自定义方法提供与传统 RPC API 相同的设计自由度，可用于实现常见的编程模式，例如数据库事务或数据分析。

### 示例
#### Gmail API
* API 服务：gmail.googleapis.com
    * 用户集合：users/*。每个用户都拥有以下资源。
        * 消息集合：users/*/messages/*。
        * 线程集合：users/*/threads/*。
        * 标签集合：users/*/labels/*。
        * 变更历史记录集合：users/*/history/*。
        * 表示用户个人资料的资源：users/*/profile。
        * 表示用户设置的资源：users/*/settings。

#### Cloud Pub/Sub API
* API 服务：pubsub.googleapis.com
    * 主题集合：projects/*/topics/*。
    * 订阅集合：projects/*/subscriptions/*。

#### Cloud Spanner API
* API 服务：spanner.googleapis.com
    * 实例集合：projects/*/instances/*。
        * 实例操作的集合：projects/*/instances/*/operations/*。
        * 数据库的集合：projects/*/instances/*/databases/*。
        * 数据库操作的集合：projects/*/instances/*/databases/*/operations/*。
        * 数据库会话的集合：projects/*/instances/*/databases/*/sessions/*。
## 仿照设计流程进行API设计
### 资源
* API 服务：ims.cloudapi.com
    * 项目集合: projects/*
        * 环境集合: projects/*/environments/*
            * 配置集合: projects/*/environments/*/configs/*
            * 资源集合: projects/*/environments/*/resources/*
        * 账号集合: projects/*/accounts/*
        * 成员集合: projects/*/members/*

* API 服务：messenger.cloudapi.com
    * 消息服务集合: provider/*
        * 频道集合: provider/*/channels/*
            * 消息集合: provider/*/channels/*/messages/*
            * 成员集合: provider/*/channels/*/members/*
        * 用户集合: provider/*/users/*
            * 消息集合: provider/*/users/*/messages/*
        
* API 服务：cmdb.cloudapi.com
    * CSP集合: csps/*
        * 服务集合: csps/*/services/*
            * 实例集合: csps/*/services/*/instances/*
                * 操作集合: csps/*/services/*/instances/*
        * 用户集合: provider/*/users/*
            * 消息集合: provider/*/users/*/messages/*