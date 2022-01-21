# microservice-platform

## A-项目说明

个人博客：www.fortuna7.top

该项目为微服务平台设计与实现的项目文档。

主要针对解决微服务和业务开发时常见的**非功能性需求**。

1.  [项目设计.md](a-introduction/项目设计.md) 
2.  [项目细节.md](a-introduction/项目细节.md) 

##  B-使用说明

启动模块：

1. djj-uaa(User Account and Authentication，用户账户和认证)
2. user-center
3. djj-gateway
4. back-web

项目编译：`mvn clean package -Dmaven.test.skip=true`

Nacos控制台启动：`sh bin/startup.sh -m standalone`  ｜ `sh bin/shutdown.sh`

Sentinel控制台启动：`java -jar -Dserver.port=6999 sentinel-dashboard.jar`

Redis控制台启动：`redis-server`

日志存放位置：`../logs`

**文档：**

-  [环境说明.md](b-usage/环境说明.md) 
-  [接口说明.md](b-usage/接口说明.md) 
-  [开发说明.md](b-usage/开发说明.md) 

## C-模块说明

- djj-uaa：uaa-server:8077
- djj-gateway：sc-gateway:8099
- djj-business：(:9001+)
  - user-center：user-center:9001
- djj-register
  - nacos：8848
- djj-sentinel：6999
- djj-web：
  - djj-back-end：9000

**自定义spring-boot-starter：**

-  [auth-client-spring-boot-starter.md](c-module/自定义spring-boot-starter/auth-client-spring-boot-starter.md) 
-  [common-core.md](c-module/自定义spring-boot-starter/common-core.md) 
-  [loadbalancer-spring-boot-starter.md](c-module/自定义spring-boot-starter/loadbalancer-spring-boot-starter.md) 
-  [redis-spring-boot-starter.md](c-module/自定义spring-boot-starter/redis-spring-boot-starter.md) 

**项目模块：**

-  [djj-gateway.md](c-module/djj-gateway.md) 
-  [djj-uaa.md](c-module/djj-uaa.md) 
-  [djj-web.md](c-module/djj-web.md) 

## D-技术预研

-  [elk.md](d-pre-research/elk.md) 
-  [metrics监控.md](d-pre-research/metrics监控.md) 
-  [mysql数据库.md](d-pre-research/mysql数据库.md) 
-  [redis.md](d-pre-research/redis.md) 

## E-功能说明

以下为该项目的一些功能实现说明：

-  [单点登录.md](e-function/单点登录.md) 
-  [登陆认证.md](e-function/登陆认证.md) 
-  [多租户.md](e-function/多租户.md) 
-  [分布式功能.md](e-function/分布式功能.md) 
-  [分布式日志链路追踪.md](e-function/分布式日志链路追踪.md) 
-  [分布式锁.md](e-function/分布式锁.md) 
-  [缓存操作.md](e-function/缓存操作.md) 
-  [前后端分离.md](e-function/前后端分离.md) 
-  [审计日志.md](e-function/审计日志.md) 
-  [alibaba-nacos.md](e-function/alibaba-nacos.md) 
-  [alibaba-sentinel.md](e-function/alibaba-sentinel.md) 
-  [jwt的RSA非对称密钥生成.md](e-function/jwt的RSA非对称密钥生成.md) 

## F-系统设计

-  [服务认证架构设计.md](f-system-design/服务认证架构设计.md) 
-  [架构设计方法论.md](f-system-design/架构设计方法论.md) 
-  [监控架构设计.md](f-system-design/监控架构设计.md) 
-  [系统幂等性.md](f-system-design/系统幂等性.md) 
-  [项目系统设计.md](f-system-design/项目系统设计.md) 

## G-技术说明

-  [前端文档.md](g-technology/前端文档.md) 
-  [bcrypt.md](g-technology/bcrypt.md) 
-  [docker.md](g-technology/docker.md) 
-  [feign.md](g-technology/feign.md) 
-  [knife4j.md](g-technology/knife4j.md) 
-  [kubernetes.md](g-technology/kubernetes.md) 
-  [mybatisplus.md](g-technology/mybatisplus.md) 
-  [oauth2.md](g-technology/oauth2.md) 
-  [OIDC.md](g-technology/OIDC.md) 
-  [redis.md](g-technology/redis.md) 
-  [spring-webflux.md](g-technology/spring-webflux.md) 
-  [spring.md](g-technology/spring.md) 
-  [springcloud.md](g-technology/springcloud.md) 
-  [springsecurity-架构.md](g-technology/springsecurity-架构.md) 
-  [springsecurity.md](g-technology/springsecurity.md) 
-  [transmittable-threadlocal.md](g-technology/transmittable-threadlocal.md) 

## H-其它

banner.txt logo来源：https://devops.datenkollektiv.de/banner.txt/index.html

-  [常用日志框架.md](h-other/常用日志框架.md) 
-  [架构的演变.md](h-other/架构的演变.md) 
-  [文档工具.md](h-other/文档工具.md) 
-  [项目设计流程.md](h-other/项目设计流程.md) 
-  [banner.md](h-other/banner.md) 
-  [develop-tools.md](h-other/develop-tools.md) 

## Z-更新说明

 [难点记录.md](z-project-daily/issue/难点记录.md) 

**ISSUE**

-  [issue-2022-01.md](z-project-daily/issue/issue-2022-01.md) 
-  [issue-2021-12.md](z-project-daily/issue/issue-2021-12.md) 

**更新日志**

- [record-2022-01.m d](z-project-daily/record-2022-01.md) 
-  [record-2021-12.md](z-project-daily/record-2021-12.md) 