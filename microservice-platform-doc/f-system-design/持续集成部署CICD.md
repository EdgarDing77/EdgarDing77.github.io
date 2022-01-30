# 持续集成部署CICD

## Introduction

- 持续集成：从编码到构建再到测试的反复过程
- 持续交付：在持续集成后，获得外部对软件的反馈再通过“持续集成”进行优化的过程。
- 持续部署：将交付的产品，快速且安全交付给用户使用。

## CICD设计

持续集成（Continuous integration，CI）：持续编译、测试、打包。

持续交付（Continuous delivery，CD）：代码在任何时候都是可部署的，并且适配不同的环境自动部署。

企业CICD流水线参考：

1. 开发提交代码 -> GitLab
2. GitLab 通过 WebHook 触发Jenkins构建
3. Jenkins 跑构建流程
4. 更新 Harbor 的镜像
5. 通知K8s触发更新服务

![img](https://cdn.jsdelivr.net/gh/edgarding77/microservice-platform-doc@latest/image/system-design/cicd_show.png)

具体部署参考：[部署说明](../b-usage/部署说明.md)