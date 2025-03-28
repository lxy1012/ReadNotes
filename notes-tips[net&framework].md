# 笔记

## 框架

### MVC：
核心目标-分离业务逻辑、数据和界面，提升代码可维护性

- Model：管理数据、业务逻辑

	- View：呈现数据，被动接受Model更新

		- Controller：接收用户输入，协调Model和 View，不直接处理业务逻辑

- 数据流：用户操作->Controller->更新Model->通知View->刷新界面

- 优点：
职责清晰-分层明确，适合团队协作
可调试性：Model和Controller可独立测试
灵活性：View和Model解耦，可复用View

- 缺点：
Controller臃肿-复杂逻辑易堆积
View和Model耦合：View可能直接依赖Model的数据结构
数据同步问题：需手动更新View，易出错【如多个View依赖同一Model】

### MVVM：
核心目标-通过数据绑定简化UI逻辑，实现数据驱动视图

- 在MVC基础上引入了ViewModel
通过数据绑定简化了View与Model的交互

	- Model：管理数据和业务逻辑

		- View：纯UI层，声明式绑定ViewModel

			- ViewModel：暴露数据与命令，处理View逻辑，通过绑定与View自动同步

- 数据流：双向自动绑定
用户操作->View触发命令->ViewModel更新Model->数据绑定->View自动刷新

- 优点：
开发效率高：双向数据绑定减少手动DOM操作
低耦合：Vie不依赖Model，仅绑定ViewModel的抽象接口
可测试性：ViewModel独立于UI，易于单元测试

- 缺点：
调式困难：数据绑定隐蔽，问题溯源复杂
性能开销：过渡绑定可能导致内存泄露或性能下降
学习成本：需理解响应式编程和绑定机制

### ECS：
核心目标-通过组合而非继承实现高灵活性的对象管理，优化性能

- Entity：唯一标识符，作为Component的容器，无实际逻辑

	- Component：纯数据对象。描述实体属性，如Position、Health

		- System：处理特定Component的逻辑，如PhysicsSystem处理移动

- 数据流：通过System处理Component的数据
System遍历相关Component->修改数据->影响Entity行为

- 优点：
灵活性高：动态组合Component，避免继承链僵化
性能优化：数据连续存储SOA，利于缓存和并行计算
解耦彻底：System和Component独立修改，拓展性强

- 缺点：
设计复杂度：需提前规划Component和System的职责
过渡碎片化：大量小型Component可能增加管理难度
使用领域窄：主要使用游戏或高性能系统，通用性较低

- Entities框架

	- 1.Entity

	- 2.Context环境--用来创建和销毁Entity,可以使用它来过滤出感兴趣的Entity

	- 3.Group组--在Context中可以对Entity进行快速过滤;
Groups拥有以下事件-OnEntityAdd，Removed和Updated来直接响应Entity的变化

	- 4.Matcher--通过Code Generator自动生成，通常用于从Context中获取所感兴趣的Groups--可以理解为筛选器

	- 5.Collector收集器--提供了一种简单的方法来处理Group中Entity变化的反应

	- 6.System：
有四种不同类型的System
a.IInitializeSystem：执行一次
b.IExecuteSystem：每帧执行
c.ICleanupSystem：在别的systems完成后每帧执行
d.ReactiveSystem：当Group有变化时执行

### 以上三个框架关键区别

- 核心思想：
1:分层解耦
2：数据驱动视图
3：组合优于继承

- 数据流：
1:单向-Controller中介
2：双向同步
3：System驱动数据变更

- 耦合度：
1:View依赖Model
2:View与Model完全解耦
3:完全解耦

- 性能：
1：中等
2：可能受绑定影响
3：高-适合并行和缓存优化

## 设计模式：23种

### 创建模式：5种

- 工厂模式
抽象工厂模式
单例模式
建造者模式
原型模式

### 结构模式：7种

- 适配者模式
装饰器模式
代理模式
外观模式
桥接模式
组合模式
享元模式

### 关系模式：11种

- 通过父子类的关系进行实现：2种

	- 策略模式
模板方法模式

- 两个类之间：4种

	- 观察者模式
迭代子模式
责任链模式
命令模式

- 类的状态：2种

	- 状态模式
备忘录模式

- 通过中间类：3种

	- 访问者模式
中介者模式
解释器模式

### 面对对象常见的设计原则

- 单一职责：数据职责、行为/业务职责

- 开闭：对拓展开、对修改关

- 里氏替换：使用父类的地方都可以使用子类对象

- 依赖倒置原则：依赖抽象，依赖接口；而不依赖具体、不依赖细节

- 接口隔离：复合接口类过于庞大需隔离分开

- 最少知识原则/迪米特法则：复合交叉引用时增加中间层，减少类的耦合性---中介者模式

- 少用继承多用组合---ECS核心思想

## 网络↓

### 传输层：可靠/不可靠

- TCP协议：传输控制协议

	- 特点：
面向连接--通信前"三次握手"建立可靠连接，结束时“四次挥手”断开
可靠性：确保数据包按序到达，支持重传机制、流量控制和拥塞控制
数据完整性：校验和机制检测数据损坏，自动修复

	- 缺点：
连接建立和状态维护带来额外开销，延迟较高
不适合实时性要求极高的场景
适用场景：
文件传输、网页加载

- UDP协议

	- 特点：
无连接--无需预先建立连接，直接发送数据
轻量快速：头部开销小-仅8字节，传输效率高
不可靠性：不保证数据到达、顺序或完整性

	- 缺点：
数据可能丢失或乱序，需应用层自行处理可靠性
易受网络拥塞影响
适用场景：
实时音视频、DNS查询

- KCP协议
核心思想：在UDP基础上实现可靠、低延迟的数据传输，牺牲带宽换取更快的响应速度，适用于弱网环境
确保弱网环境下的可靠低延迟传输

	- 设计目标：
解决TCP在高延迟、高丢包网络下的性能缺陷，如游戏、实时音视频场景

	- 核心机制
ARQ自动重传：通过选择性重传[仅重传丢失的包]减少无效重传，对比TCP的累计确认更高效
RTO动态调整：根据网络状态动态计算超时重传时间，避免固定RTO导致的延迟堆积
无拥塞控制：放弃TCP的慢启动和拥塞窗口机制，直接由应用层控制发送速率，减少延迟波动
流量控制：支持手动配置发送窗口[snd_wnd]和接收窗口[rcv_wnd]，灵活适应不同场景

- QUIC协议

### 表示层

- Json/XML

### 应用层

- Http协议：超文本传输协议

	- 版本演进：
HTTP/1.1：持久连接、管道化请求
HTTP/2：多路复用、头部压缩、服务器推送
HTTP/3：基于QUIC协议，解决队头阻塞，提升移动网络性能

		- BestHttp

	- 特点：
请求-响应模型：客户端发起请求，服务器返回响应
无状态：每次请求独立，服务器不保留客户端状态--需Cookie/Session实现状态管理
基于TCP：默认使用TCP保证可靠性，但HTTP/3改用QUIC（基于UDP）以减少延迟

	- 缺点：单向通信，服务器无法主动推送数据-需轮询或WebSocket
适用场景：
Web API、资源加载

- WebSocket协议

	- 特点：
全双工通信：建立连接后，客户端和服务器可随时双向发送数据
基于TCP：底层依赖TCP协议，通过HTTP协议升级握手--101状态码切换协议
低延迟：避免HTTP的频繁连接开销，适合实时交互

	- 缺点：
需维护长连接，服务器资源消耗较高
兼容性依赖服务器支持
适用场景：
实时聊天、游戏同步

- Protobuf协议：数据序列化协议
核心思想：通过二进制编码和预定义Schema,实现高效的结构化数据和序列化，代替Json/xml

	- 设计目标：提升数据压缩率、解析速度及跨语言兼容性

	- 特点：
数据体积小--二进制编码[可读性差]，数据紧凑
数据解析快，直接内存映射
跨语言支持：通过编译器生成Java/C++/Python等代码，实现多语言无缝交互
向后兼容性：支持字段增删，新旧版本可共存
灵活性相比Json/XML低，需预定义Schema

	- 缺点：
Schema管理成本：需维护.proto文件并同步更新各端代码
调试困难：需借助工具解码
非实时性需求不适用：简单配置类数据用Json更便捷

	- KCP与Protobuf的协同适用：
应用层数据[游戏指令]->Protobuf序列化为二进制->通过KCP协议传输->接收方反序列化

