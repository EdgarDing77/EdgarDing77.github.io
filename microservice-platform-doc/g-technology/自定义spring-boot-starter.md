# 自定义spring-boot-starter

## Introduction

「Why」为了简化项目的开发配置，抑或是多个项目复用同一组件（如MyBatis等），可以通过开发自定义的`starter`，来减少这种多次复制粘贴，仅需自定义配置即可。

`starter`的实现：虽然我们每个组件的`starter`实现各有差异，但是它们基本上都会使用到两个相同的内容：`ConfigurationProperties`和`AutoConfiguration`。因为`Spring Boot`提倡“**「约定大于配置」**”这一理念，所以我们使用`ConfigurationProperties`来保存我们的配置，并且这些配置都可以有一个默认值，即在我们没有主动覆写原始配置的情况下，默认值就会生效。除此之外，`starter`的`ConfigurationProperties`还使得所有的配置属性被聚集到一个文件中（一般在`resources`目录下的`application.properties`）。

## 原理

### Bean的自动配置

自动配置是通过标注有`@Configuration`注解实现，其他的`@Conditional*`注解用于约束何时应用自动配置，同时SpringBoot也提供了一系列的条件注解来灵活的进行自动配置。

`SpringApplication.run()`中会初始化META/spring.factories文件中的ApplicationContext并将Bean注入到ApplicationContext中。

*SpringBoot加载详解：https://fortuna7.top/archives/spring-he-xin-gai-nian-shu-li*

### 相关注解

#### 配置注解

`@ConfigurationProperties`：使开发人员可以轻松地将`.properties`和`yml`文件映射到一个对象中。编写Properties，应使用唯一的名称空间。不要使用`Spring Boot`的名称空间（如server，management，spring，等）。所以应在所有配置键前面加上自己的名称空间。如`djj.log.type`。

有着如下属性：

- prefix：配置前缀项
- value：绑定到此对象的属性前缀，prefix的同义

`@EnableConfigurationProperties`：作用是为了使`@ConfigurationProperties`注解的类生效。

`@Configuration`：标注，并加载到Spring容器中。

`@RefreshScope`：以这种方式注释的 Bean 可以在运行时刷新，任何使用它们的组件都将在下一次方法调用时获得一个新实例，完全初始化并注入所有依赖项。

#### **常用条件注解**

以下来源于：org.springframework.boot2.5.7

- Class条件
  - ConditionalOnClass：只有当指定的类在类路径相匹配
  - ConditionalOnMissingClass
- Bean条件
  - ConditionalOnBean：但需要的Bean存在的时候
  - ConditionalOnMissingBean
- Property条件
  - ConditionalOnProperty：
  - 基于Spring的环境属性配置包括在内。使用`prefix`和`name`属性来指定应检查的属性。默认情况下，匹配任何存在且不等于false的属性。还可以使用havingValue和matchIfMissing属性创建更高级的检查。
- Resource条件
  - ConditionalOnResource：允许仅在存在特定资源时才包含配置。可以使用常见的Spring约定来指定资源。
- 其他条件：
  - ConditionalOnCloudPlatform
  - ConditionalOnExpression：允许根据SpEL表达式的结果包含配置
  - ConditionalOnJava
  - ConditionalOnJndi
  - ConditionalOnNotWebApplication
  - ConditionalOnSingleCandidate
  - ConditionalOnWarDeployment
  - ConditionalOnWebApplication

## 具体实现

### 命名规范

**模块命名规范：**

> “What’s in a nameAll official starters follow a similar naming pattern; spring-boot-starter-*, where * is a particular type of application. This naming structure is intended to help when you need to find a starter. The Maven integration in many IDEs lets you search dependencies by name. For example, with the appropriate Eclipse or STS plugin installed, you can press ctrl-space in the POM editor and type “spring-boot-starter” for a complete list.As explained in the “Creating Your Own Starter” section, third party starters should not start with spring-boot, as it is reserved for official Spring Boot artifacts.
>
> Rather, a third-party starter typically starts with the name of the project. For example, a third-party starter project called thirdpartyproject would typically be named thirdpartyproject-spring-boot-starter.”

即官方的命名格式为`spring-boot-starter-{xxxx}`，第三方自己的命名格式为`{xxxx}-spring-boot-starter`。

**配置命名规范：**

定义方式为`{XXXX}Properties`，如`djj-log-spring-boot-starter`，引入一个自定义的日子模组。

### 依赖引入

必要依赖引入：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
```

引入这个`spring-boot-configuration-processor`之后，我们编译之后就会在`META-INF`文件夹下面生成一个`spring-configuration-metadata.json`的文件。

同时，idea的配置自动提示也会根据该文件进行提示，方便配置。`Spring Boot`使用一个注释处理器来收集元数据文件（`META-INF/Spring autoconfigure metadata.properties`）中自动配置的条件。如果该文件存在，它将用于急切地筛选不匹配的自动配置，这将提高启动时间。

### 自定义配置

编写XXXProperties.java：

```java
@Getter
@Setter
@ConfigurationProperties(prefix = "djj.audit-log")
@RefreshScope
public class AuditLogProperties {
    /**
     * 是否开启审计日志
     */
    private Boolean enable = false;
    /**
     * 日志记录类型(logger/redis/db/es)
     */
    private String type;
}
```

编写XXXAutoConfigure.java：

```java
@ComponentScan(value = "top.edgarding.infrastructure.log")
@EnableConfigurationProperties({AuditLogProperties.class)
public class LogAutoConfigure {}
```

编写spring.factories：

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  top.edgarding.infrastructure.log.LogAutoConfigure
```

### 让starter生效

starter集成应用的方式有两种：

- 被动生效我们首先来看下我们熟悉的方式，通过`SpringBoot`的`SPI`的机制来去加载我们的 starter。我们需要在`META-INF`下新建一个`spring.factories`文件`key`为`org.springframework.boot.autoconfigure.EnableAutoConfiguration`，value是我们的`XXXAutoConfiguration` 全限定名。
- 主动生效在`starter`组件集成到我们的`Spring Boot`应用时需要主动声明启用该`starter`才生效，通过自定义一个`@Enable`注解然后在把自动配置类通过`Import`注解引入进来。

## Reference

官网：https://spring.io/guides/gs/spring-boot/

Github：https://github.com/spring-projects/spring-boot/tree/main/spring-boot-project/spring-boot-starters

SpringBootStarters：https://www.baeldung.com/spring-boot-starters