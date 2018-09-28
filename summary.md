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





作为F项目iOS组负责人，从零开发一款全新的app，从零组建iOS团队。负责简历筛选，面试，及工程师评级。负责确定app架构方案制定工作。在初期遇到了相继3个后续人都未能最终到岗的问题，因此在项目初期担任整个app的架构和v0.1v 版本的开发工作。


Experience in bytedance


I join to Lark team as architect. I developed the first version messages synchronization engine for Lark app, this engine help us manage the websocket connection, data transfer channel, and working threads. Then we decided use Rust language to develop an new engine which can be used in both android, iOS and PC(JS) client, I discussed with the whole team member many times, finally we have the solution that we can use 60 man days to finished this task.


Lark team is a new team, Lark is also a new app, I spend lots of time to transfer my knowledge, the experience of UML design, the principle of the OOP design，functional programing, FRP (Functional Reactive Programing), Domain-Driven Design to the team member. The team members have changed and they start to spend more time of thinking how to make their code mode flexible， more easy to be testign, and easy to be reused.

FProject

I join this team as iOS leader, our target is to develop a new app, zero to one. But I have a big problem at the begining, we  sent three offers for iOS developer but finally no one join us, so I am the only iOS developer in FProject, at the same time we have 5 android engineers. I feel a really huge pressure to me, because I am not only to be a member of iOS team, I am the iOS team. How to use my time on study all requirements, coding, discuss we 6 production manager, and working with 4 QA member?

Then we have two new iOS developer join to our team recently, other problem is how to make them warm up at the begining, and make us working as team. I talk with them and make one on one talk so that we know our goal and how to achieved it.

Finally we finished and release our first version to iOS market.
