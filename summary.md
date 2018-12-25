IBM CRL
加入盘古项目组，担任JAVA高级开发工程师，基于IBM SOA产品为用户提供企业级分布式解决方案，使用WebSphere WPS作为应用服务器，基于SCA框架将用户需求以组建的形式进行开发，使用BPEL引擎实现业务流程建模，并驱动业务组件工作。并使用ESB消息服务实现多服务的消息通信，从而降低服务间的耦合并提高分布式部署的灵活性。使用Lucence搜索引擎实现检索服务降低数据库的负担，实现简单的数据聚合和检索功能。

GOOD 
作为Java工程师，负责J2ME应用的研发实现push email应用，通过Socket连接与服务器进行实时的数据同步，井用户的邮件数据通过加密方式同步到终端。09年开始学习和研究android研发，在HTC第一台设备上市的同时，用一周的时间为公司提供了第一个Android平台的客户端Demo，并最终与3位同时共同开发完成了第一个版本的Android客户端。2010年开始加入公司iOS组，负责iOS版本产品的研发，并参与解决由于iOS平台支持后台运行后带来的稳定性问题。

阿莫多
2012年离开GOOD加入阿莫多公司，使用python搭建爬虫系统收集LBS信息，并使用Elasticsearch实现LBS数据的搜索和推荐功能为用户提供实时的停车场车位信息。使用MongoDB实现数据的聚合计算，为停车场提供数据检索及报表服务。并使用Erlang语言开发客户端通过4G无线网络同步停车场数据到服务端完成数据获取。


与同事共同讨论和搭建推荐系统，通过建立小区分组，从而实现房屋信息的数据去重和信息的辩真工作。参与推荐系统模型参数的制定和优化，使用SVMRank模型实现房源信息的个性化推荐。
负责第一个版本的iOS客户端的开发，并负责面试和组建iOS团队。

bytedance 工作经历



Lark

1）研发核心数据同步引擎，支持自由应用层通信协议，支持数据帧化传输，自适应调整传输速率，连接超时空置，实现数据多通道路由（http request/socket connection）
2）引入组件管理框架，支持消息驱动模式，Ioc依赖注入模式，解决模块集成管理问题，使得各个子项目开发人员可以并行开发且避免静态编译时的耦合依赖。
3）设计中间接口层，引入抽象工厂模式并结合IOC容器实现视图层/业务逻辑层与底层的分离，并实现应用程序可以在runtime时灵活切换底层实现。（由原有Swift native引擎切换到Rust版本）最终实现一套核心代码三端共享。
4) 研究Swift语言中的特性，引入相应式函数编程RxSwift模式，并将所得的经验传给team。
5）负责作为团队的scrum教练，帮助团队解决日常在执行过程中遇到的问题。
6）引导团队顺利完成Spring planning metting, Task splitting and Sprint Retrospective Meeting, 从中团队逐渐增加了自取动能力，并通过持续的回顾总价提升自身的能力。
7）参与架构调整，组建库设计和抽取工作包括图片缓存模块，图片压缩，端监控，日志模块以及异常监控模块。
FProject
1）根据产品特点选择继承头条基础组建快速搭建框架，框架主要解决网络通信，图片缓存，音视频播放，用户行为收集，线程控制等逻辑。
2）实现核心业务模块的设计，以及开发工作，与产品，QA，后端合作最终将第一个版本的app提交到苹果商店并通过审核。
3）负责面试工作，为团队寻找优秀的开发人员。
4）协助并引导新成员第一时间了解熟悉头条的技术栈，底层库的设计以及现有代码，最快速度进入状态。
5）制定app核心技术指标以及指标的基线，逐步提升app的性能和用户体验。







Experience in bytedance


I join to Lark team as architect. I developed the first version messages synchronization engine for Lark app, this engine help us manage the websocket connection, data transfer channel, and working threads. Then we decided use Rust language to develop an new engine which can be used in both android, iOS and PC(JS) client, I discussed with the whole team member many times, finally we have the solution that we can use 60 man days to finished this task.


Lark team is a new team, Lark is also a new app, I spend lots of time to transfer my knowledge, the experience of UML design, the principle of the OOP design，functional programing, FRP (Functional Reactive Programing), Domain-Driven Design to the team member. The team members have changed and they start to spend more time of thinking how to make their code mode flexible， more easy to be testign, and easy to be reused.

FProject

I join this team as iOS leader, our target is to develop a new app, zero to one. But I have a big problem at the begining, we  sent three offers for iOS developer but finally no one join us, so I am the only iOS developer in FProject, at the same time we have 5 android engineers. I feel a really huge pressure to me, because I am not only to be a member of iOS team, I am the iOS team. How to use my time on study all requirements, coding, discuss we 6 production manager, and working with 4 QA member?

Then we have two new iOS developer join to our team recently, other problem is how to make them warm up at the begining, and make us working as team. I talk with them and make one on one talk so that we know our goal and how to achieved it.

Finally we finished and release our first version to iOS market.
