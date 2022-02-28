# microservice-platform

## A-项目说明

个人博客：www.fortuna7.top

该项目为微服务平台设计与实现的项目文档。

**项目概述：**采用前后端分离的微服务架构，主要针对解决微服务和业务开发时常见的**非功能性需求**。基于Spring Boot2.5.7构建，Spring Cloud Finchley、Spring Cloud Alibaba实现微服务，定制Spring Security实现RBAC、jwt和oauth2的权限认证方案，提供多租户隔离，适用于B端和C端用户。

1.  [项目设计.md](a-introduction/项目设计.md) 
2.  [项目细节.md](a-introduction/项目细节.md) 

## B-使用说明

**开发启用：**

1. sh work.sh
2. 启动如下核心模块

**核心模块：**

1. djj-uaa(User Account and Authentication，用户账户和认证):8077
2. djj-business
   - user-center:9001
   - file-center:9005
   - search-center
     - search-client
     - search-server:9010
3. djj-gateway
   - sc-gateway:8099
4. back-web 9000

**拓展模块：**

1. djj-monitor
   - sc-admin:8150
   - log-center:8200

**命令使用：**

项目编译：`mvn clean package -Dmaven.test.skip=true`

Nacos控制台启动：`sh bin/startup.sh -m standalone`  ｜ `sh bin/shutdown.sh`

Sentinel控制台启动：`java -jar -Dserver.port=6999 sentinel-dashboard.jar`

Redis控制台启动：`redis-server`

docker打包：参考docker文档

日志存放位置：`../logs`

脚本启动或关闭nacos和redis：`sh work.sh`

**账号密码：**

1. back-web:admin/123456
2. sc-admin监控:admin/admin

**Spring-Boot自动提示：**将项目重新编译，并修改IDEA配置，搜索`Annotation Processor`并设置`Enable annotation processing`。

**文档：**

-  [部署说明.md](b-usage/部署说明.md) 
-  [环境说明.md](b-usage/环境说明.md) 
-  [接口说明.md](b-usage/接口说明.md) 
-  [开发说明.md](b-usage/开发说明.md) 
-  [前后端分离.md](b-usage/前后端分离.md) 

## C-模块说明

- djj-uaa：uaa-server:8077
- djj-gateway：sc-gateway:8099
- djj-business：(:9001+)
  - user-center:9001
  - file-center:9005
- djj-register
  - nacos：8848
- djj-sentinel：6999
- djj-monitor：
  - log-cneter：8200
  - sc-admin：8150

- djj-web：
  - back-end：9000

**必要启动模块：**

1. djj-uaa 授权中心
2. djj-web:back-end 后台前端页面
3. djj-gateway:sc-gateway Spring Cloud Gateway统一网关
4. djj-business:user-center 用户中心 授权需要RPC该模块
5. djj-register:nacos 配置中心、服务注册、服务发现

**拓展模块：**

- djj-monitor:sc-admin 微服务监控
- djj-business
  - file-center 文件中心（使用gitee作为图床实现）

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
-  [spring核心概念梳理.md](d-pre-research/spring核心概念梳理.md) 

## E-功能说明

以下为该项目的一些功能实现说明：

-  [单点登录.md](e-function/单点登录.md) 
-  [登陆认证.md](e-function/登陆认证.md) 
-  [多租户.md](e-function/多租户.md) 
-  [分布式事务.md](e-function/分布式事务.md) 
-  [分布式日志链路追踪.md](e-function/分布式日志链路追踪.md) 
-  [分布式锁.md](e-function/分布式锁.md) 
-  [缓存操作.md](e-function/缓存操作.md) 
-  [前后端分离.md](e-function/前后端分离.md) 
-  [日志中心.md](e-function/日志中心.md) 
-  [审计日志.md](e-function/审计日志.md) 
-  [微服务监控.md](e-function/微服务监控.md) 
-  [文件中心.md](e-function/文件中心.md) 
-  [alibaba-nacos.md](e-function/alibaba-nacos.md) 
-  [alibaba-sentinel.md](e-function/alibaba-sentinel.md) 
-  [jwt的RSA非对称密钥生成.md](e-function/jwt的RSA非对称密钥生成.md) 

## F-系统设计

-  [持续集成部署CICD.md](f-system-design/持续集成部署CICD.md) 
-  [分布式ID生成器.md](f-system-design/分布式ID生成器.md) 
-  [服务认证架构设计.md](f-system-design/服务认证架构设计.md) 
-  [架构设计方法论.md](f-system-design/架构设计方法论.md) 
-  [监控架构设计.md](f-system-design/监控架构设计.md) 
-  [系统幂等性.md](f-system-design/系统幂等性.md) 

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
-  [spring-webflux.md](g-technology/spring-webflux.md) 
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

**项目记录：**

-  [record-2022-01.md](z-project-daily/record-2022-01.md) 
-  [record-2021-12.md](z-project-daily/record-2021-12.md)  
-  [record-2022-02.md](z-project-daily/record-2022-02.md) 
