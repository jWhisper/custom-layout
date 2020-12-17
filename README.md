# GO Project Standard Layout

## api
proto文件,hook到一个统一的api仓库
https://www.bookstack.cn/read/API-design-guide/API-design-guide-README.md
> FieldMask, 增量更新
1. 方便跨部门协作
2. 版本管理,基于git控制
3. 规范化检查, api lint
4. api design review
5. 权限管理, owners目录
### api/errors
使用pb产生可传递,可自定义的grpcerrors
service error --> grpc errors --> service error(插件)

## cmd
程序启动,注销,初始化
一个app可以分4种:
1. admin面向运营的服务,权限更高
2. service:对内的微服务,仅接受来自内部其他服务或者网关的请求,比如暴露的grpc接口只对内服务
3. interface: 对外的BFF服务,接受来自用户的请求,比如暴露了http/grpc接口
4. job: 流式任务,常驻后台
5. task: 定时任务,类似cronjob,部署到task托管平台.

## internal
仅在该项目使用,无法被其他项目导入
1. service
     DTO
api ----> service(类似DDD的application)
service服务的编排,组合,实现DTO->DO

2. biz
         DTO
service ----> biz(类似DDD的domain)
biz实现具体业务逻辑,聚合服务
**定义DO**,贫血模型
**定义do的repo接口**, 使用依赖注入data层的具体实现

3. data
实现上层biz定义的具体的持久化方法; 与dao比,更偏向于业务
~~4. dao~~
定义PO,实现DTO->PO
https://github.com/facebook/ent


## configs
1. 环境变量
region、Zone、Enviroment、Color、Discovery、Cluster、AppID、HOST等之类的环境信息，就是通过在线运行时平台打入到容器或者物理机，供kit库读物使用
2. 静态变量
数据库的配置信息
3. 动态变量
在线开关，来控制业务的一些简单策略，会频繁的调用和使用，我们把这类是基础类型(int, bool)等配置,用于可以动态变更业务流的收归一起,同时可以考虑结合类似 https://pkg.go.dev/expvar
4. 全局变量
各类组件,中间件都有大量的默认配置或者指定配置.

**配置文件pb**和配置初始化options要解耦

自动回滚

## test
> Google软件测试之道

使用docker的染色测试,面向用户的大中型测试,kit的小型单元测试
Subtests + Gomock

1. api(推荐)
yapi测试框架
2. data
docker compose
3. biz
gmock
4. service
依赖biz的实现, 构建biz的实现类并传入,进行单元测试

---

## 生命周期管理
errgroups
wires
https://github.com/go-kratos/kratos/blob/v2/app.go

## Function options
可选参数

## protocol buffer
https://github.com/protocolbuffers/protobuf/blob/master/src/google/protobuf/wrappers.proto
可选类型
Warpper 类型的字段，即包装一个 message，使用时变为指针。
