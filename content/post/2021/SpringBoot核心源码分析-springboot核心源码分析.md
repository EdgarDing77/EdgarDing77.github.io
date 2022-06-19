---
title: SpringBoot核心源码分析
date: 2020-10-17 14:55:50.0
updated: 2021-09-15 14:29:45.784
categories: 
tags: [Spring]
---



# SpringBoot 核心源码分析

## convention over configuration 约定优于配置

SprinBoot 核心特性：约定优于配置

> 举一个具体一点的例子，这个理念我们其实一直都在遵循并使用，比如我们在 MVC 项目的开发中会把实体类放到 entity 目录下、数据接口层会定义在 dao 目录下、控制器会定义在 controller 目录下，这就是大家都遵循的规矩，如果在项目中遵守命名规范就可以显著地减少系统需要的配置，不然目录名、包名乱取名字这些都会使得配置难度增大。这个理念在很多地方都有使用，比如我们常用的 SpringMVC 框架就能够找到很多设计是遵循这个理念，想要详细了解的可以去查一下`convention over configuration`的相关资料。
>
> 也就是说，SpringBoot 遵循“约定优于配置”这个理念，但是这个理念绝对不是 SpringBoot 所独有的，这个理念也不是由 Spring Boot 框架所创造出来的概念，Spring Boot 能够精简配置提升开发者的工作效率，并不仅仅因为 “约定优于配置” 这个特性，而是因为 Spring Boot 框架中众多的自动化配置类及相关优秀的程序设计导致的，这些自动化配置类在类路径下`META-INF/spring.factories`文件中，通过`@EnableAutoConfiguration`注解加载到容器中并发挥作用，从而使得开发这在使用 SpringBoot 框架甚至可以做到零配置就能够快速构建出所需的应用。

## SpringBoot 依赖管理

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

对spring-boot-starter-parent按住command进入查看其源码：

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.3.4.RELEASE</version>
</parent>
```

发现该项目依赖于一个父项目 spring-boot-starter-parent，再进入该项目里面：

```xml
<properties>
  <activemq.version>5.15.13</activemq.version>
  <antlr2.version>2.7.7</antlr2.version>
  <appengine-sdk.version>1.9.82</appengine-sdk.version>
  <artemis.version>2.12.0</artemis.version>
  <aspectj.version>1.9.6</aspectj.version>
  <assertj.version>3.16.1</assertj.version>
  <atomikos.version>4.0.6</atomikos.version>
  <awaitility.version>4.0.3</awaitility.version>
  <bitronix.version>2.1.4</bitronix.version>
  <build-helper-maven-plugin.version>3.1.0</build-helper-maven-plugin.version>
  <byte-buddy.version>1.10.14</byte-buddy.version>
  <caffeine.version>2.8.5</caffeine.version>
  <cassandra-driver.version>4.6.1</cassandra-driver.version>
  <classmate.version>1.5.1</classmate.version>
  <commons-codec.version>1.14</commons-codec.version>
  <commons-dbcp2.version>2.7.0</commons-dbcp2.version>
  <commons-lang3.version>3.10</commons-lang3.version>
  <commons-pool.version>1.6</commons-pool.version>
  <commons-pool2.version>2.8.1</commons-pool2.version>
  <couchbase-client.version>3.0.8</couchbase-client.version>
  <db2-jdbc.version>11.5.4.0</db2-jdbc.version>
  <dependency-management-plugin.version>1.0.10.RELEASE</dependency-management-plugin.version>
  <derby.version>10.14.2.0</derby.version>
  <dropwizard-metrics.version>4.1.12.1</dropwizard-metrics.version>
  <ehcache.version>2.10.6</ehcache.version>
  <ehcache3.version>3.8.1</ehcache3.version>
  <elasticsearch.version>7.6.2</elasticsearch.version>
```

进入之后可以发现，该 pom 文件中定义了大量的依赖信息，如 commons 相关依赖、log 相关依赖、数据库相关依赖、Spring 相关依赖、elastic search 搜索引擎相关依赖、消息队列相关依赖等等，J2EE 项目开发中所涉及到的大部分功能场景的依赖都定义在这个 pom 文件中，这里就是 Spring Boot 项目依赖的版本管理中心。

版本管理中心已经默认帮我们配置好了大部分依赖的版本信息，这些版本信息随着 Spring Boot 版本的更新也会随之更改，这个设计让我们在今后导入依赖时不需要写版本，直接使用其默认版本即可，当然也可以自行修改版本号，但是没有在 dependencies 里默认管理的依赖仍然需要声明其版本号。

结合前一小节中的“约定优于配置”特性，我们可以这样理解：Spring Boot 默认帮我们设置了默认编码、默认 JDK 版本及 maven 编译的默认设置，同时维护了一套项目依赖的配置，需要使用哪个依赖直接导入即可并不需要声明其版本号。这就是 Spring Boot 其中的一个开发约定，咱们如果认可这个约定就可以减少一些基本配置和很多依赖配置，如果不认可这种方式也可以自行配置，这些配置会将默认配置覆盖掉。

## SpringBoot 主程序类

```java
package com.ttt.chacha.chacha;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@MapperScan("com.ttt.chacha.chacha.dao")
@SpringBootApplication // 标注这是一个springboot应用
public class ChachaApplication {

    public static void main(String[] args) {
        SpringApplication.run(ChachaApplication.class, args);
    }

}
```

### @SpringBootApplication

源码内容（去注解）：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {


   @AliasFor(annotation = EnableAutoConfiguration.class)
   Class<?>[] exclude() default {};


   @AliasFor(annotation = EnableAutoConfiguration.class)
   String[] excludeName() default {};


   @AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
   String[] scanBasePackages() default {};


   @AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
   Class<?>[] scanBasePackageClasses() default {};


   @AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
   Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;


   @AliasFor(annotation = Configuration.class)
   boolean proxyBeanMethods() default true;

}
```

@Target（ElementType.TYPE）：类、接口（包括注解类型）和 enum 声明

@Retention（RetentionPolicy.RUNTIME）：运行时注解

@Documented：注解添加到Java doc中

@Inherited：允许继承

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { 
  		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```

这三个为Spring Boot框架的自定义注解：

@SpringBootConfiguration：SpringBoot 配置注解

@EnableAutoConfiguration：启用自动配置注解

@ComponentScan：组件自动扫描注解

可以简单的将 @SpringBootApplication 注解理解为 @SpringBootConfiguration 、@EnableAutoConfiguration 和 @ComponentScan 这三个注解的组合注解，如果在主程序类中不使用 @SpringBootApplication 注解，在主程序类上直接标注 @SpringBootConfiguration、@EnableAutoConfiguration 和 @ComponentScan 这三个注解的效果也是一样的。

### @SpringBootConfiguration

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}

```

主要用于定义配置类，代替XML配置文件。@SpringBootConfiguration 注解仅仅是对 @Configuration 注解进行了包装，本质上依然是 @Configuration 注解，@SpringBootConfiguration 注解是 1.4 版本中新增的注解，标注在某个类上，表示这是一个 Spring Boot 的配置类。

### @EnableAutoConfiguration

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

	/**
	 * Exclude specific auto-configuration classes such that they will never be applied.
	 * @return the classes to exclude
	 */
	Class<?>[] exclude() default {};

	/**
	 * Exclude specific auto-configuration class names such that they will never be
	 * applied.
	 * @return the class names to exclude
	 * @since 1.3.0
	 */
	String[] excludeName() default {};

}
```

代表开启自动配置功能。

除 Java 元注解外，这个注解最重要的就是 @AutoConfigurationPackage 注解 和 AutoConfigurationImportSelector 组件。

@AutoConfigurationPackage 注解源码如下：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
}
```

该注解中中包含 Spring 框架的 @Import 注解，其作用就是将标注了该注解的组件注册到 Spring 的 IOC 容器中，而导入哪些内容则由 AutoConfigurationPackages.Registrar.class 指定，即 Spring Boot 会注册自动配置包的名称，默认情况下是当前主程序类所在的包及其子包，这些包中的组件会被加载进容器中，如果是写到其他包中的组件默认是无法被扫描进来的，这也是为什么某些开发者在配置了组件后无法被加载的原因，希望大家注意。

EnableAutoConfigurationImportSelector 是整个自动配置功能的核心实现，负责返回自动配置的相关组件名称以注册至 IOC 容器中，主要实现如下：

```java
public class AutoConfigurationImportSelector
		implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware,
		BeanFactoryAware, EnvironmentAware, Ordered {
    ...省略部分代码

	@Override
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return NO_IMPORTS;
		}
        //调用 AutoConfigurationMetadataLoader.loadMetadata()方法获取自动配置元数据
		AutoConfigurationMetadata autoConfigurationMetadata =  AutoConfigurationMetadataLoader
				.loadMetadata(this.beanClassLoader);
		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(
				autoConfigurationMetadata, annotationMetadata);
		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
	}

	protected AutoConfigurationEntry getAutoConfigurationEntry(
			AutoConfigurationMetadata autoConfigurationMetadata,
			AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
        // 获取自动装配配置项
		List<String> configurations = getCandidateConfigurations(annotationMetadata,
				attributes);
        // 去重
		configurations = removeDuplicates(configurations);
        // 获取停用配置项(开发者自行设置的，排除哪些不需要自动配置的组件)
		Set<String> exclusions = getExclusions(annotationMetadata, attributes);
		checkExcludedClasses(configurations, exclusions);
        // 移除停用配置项
		configurations.removeAll(exclusions);
		configurations = filter(configurations, autoConfigurationMetadata);
		fireAutoConfigurationImportEvents(configurations, exclusions);
        // 返回所有的自动装配配置
		return new AutoConfigurationEntry(configurations, exclusions);
	}

	@Override
	public Class<? extends Group> getImportGroup() {
		return AutoConfigurationGroup.class;
	}
	//该方法判断是否开启自动配置
	protected boolean isEnabled(AnnotationMetadata metadata) {
		if (getClass() == AutoConfigurationImportSelector.class) {
            //获取 spring.boot.enableautoconfiguration 的值是 true 还是 false，默认为true
			return getEnvironment().getProperty(
					EnableAutoConfiguration.ENABLED_OVERRIDE_PROPERTY, Boolean.class,
					true);
		}
		return true;
	}
     	
    protected List<String> getCandidateConfigurations(AnnotationMetadata metadata,
			AnnotationAttributes attributes) {
        // 从META-INF/spring.factories中获取 EnableAutoConfiguration 的所有配置项
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
				getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
		Assert.notEmpty(configurations,
				"No auto configuration classes found in META-INF/spring.factories. If you "
						+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
```

getCandidateConfigurations() 方法会调用 SpringFactoriesLoader 类的 loadFactoryNames() 获取所有的自动配置类类名，源码如下：

```java
public final class SpringFactoriesLoader {
    //配置文件名称
	public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
	
    ...省略部分代码
        
	public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
		String factoryClassName = factoryClass.getName();
		return loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
	}

	private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
		MultiValueMap<String, String> result = cache.get(classLoader);
		if (result != null) {
			return result;
		}
		try {
			Enumeration<URL> urls = (classLoader != null ?
					classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
					ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
			result = new LinkedMultiValueMap<>();
			while (urls.hasMoreElements()) {
				URL url = urls.nextElement();
				UrlResource resource = new UrlResource(url);
				Properties properties = PropertiesLoaderUtils.loadProperties(resource);
				for (Map.Entry<?, ?> entry : properties.entrySet()) {
					String factoryClassName = ((String) entry.getKey()).trim();
					for (String factoryName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
						result.add(factoryClassName, factoryName.trim());
					}
				}
			}
			cache.put(classLoader, result);
			return result;
		}
		catch (IOException ex) {
			throw new IllegalArgumentException("Unable to load factories from location [" +
					FACTORIES_RESOURCE_LOCATION + "]", ex);
		}
	}
```

AutoConfigurationImportSelector 会将所有需要导入的组件以全类名的方式返回，这些组件就会被注册到 IOC 容器中。

### @ComponentScan

做过 Spring 项目的朋友一定都有用过 @Controller 、 @Service 、 @Repository 注解，查看其源码会发现，他们中有一个共同的注解 @Component，而 @ComponentScan 注解默认就会装配标识了 @Controller 、 @Service 、 @Repository 、 @Component 注解的类到 Spring IOC 容器中。

在普通的 Spring 项目开发中，会在 Spring 配置文件中编写如下配置，将对应包下的所有组件扫描并注册到容器中：

```xml
    <!-- 自动扫描 -->
    <context:component-scan base-package="com.ssm.demo.dao"/>
    <context:component-scan base-package="com.ssm.demo.service"/>
    <context:component-scan base-package="com.ssm.demo.controller"/>
```

使用注解的方式与这种 xml 配置文件方式实现的效果是相同的，@ComponentScan 注解的作用就是让 Spring 容器知道从哪些包下获取需要注册的 bean ，开发者通过注解来定义哪些包需要被自动扫描并装配，一旦指定了相应的包名， Spring 将会将在被指定的包及其子包中寻找标注了以上注解的 bean 并注册至容器中。

### SpringApplication.run()

```java
public class SpringApplication {
    ...省略部分代码
	public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch();
        // 代码执行时间监控开启
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
        // 配置 headless 属性，默认为 true
		configureHeadlessProperty();
        // 获取 SpringApplicationRunListener
		SpringApplicationRunListeners listeners = getRunListeners(args);
        // 回调所有 SpringApplicationRunListener 对象的 starting()方法
		listeners.starting();
		try {
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(
					args);
            // 创建 Environment 对象并回调 SpringApplicationRunListener 对象的 environmentPrepared() 方法
			ConfigurableEnvironment environment = prepareEnvironment(listeners,
					applicationArguments);
            // 配置 Environment
			configureIgnoreBeanInfo(environment);
            // 获取打印的 spring boot banner
			Banner printedBanner = printBanner(environment);
            // 创建ApplicationContext实例
			context = createApplicationContext();
			exceptionReporters = getSpringFactoriesInstances(
					SpringBootExceptionReporter.class,
					new Class[] { ConfigurableApplicationContext.class }, context);
            // 配置 ApplicationContext 实例
			prepareContext(context, environment, listeners, applicationArguments,
					printedBanner);
            // 刷新 ApplicationContext 实例
			refreshContext(context);
            // refresh()方法成功回调
			afterRefresh(context, applicationArguments);
            // 代码执行时间监控结束，即启动应用花费了多少时间
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass)
						.logStarted(getApplicationLog(), stopWatch);
			}
            // 回调监听处理器
			listeners.started(context);
            // 调用 Runner 回调
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, listeners);
			throw new IllegalStateException(ex);
		}

		try {
			listeners.running(context);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
}
```

#### 执行流程

SpringApplication将一个典型的spring应用启动的流程“模板化”，默认模板化后执行流程就可以满足需求了，如果有特殊需求，SpringApplication在合适的流程节点开放了一系列不同类型的扩展点，我们可以通过这些扩展点对SpringBoot程序的启动和关闭过程进行扩展。

1. 如果我们使用的是SpringApplication的静态run方法，首先需要创建一个SpringApplication对象实例。

2. - 使用SpringFactoriesLoader在应用的classpath中查找并加载所有可用的ApplicationContextInitialize
   - 使用SpringFactoriesLoader在应用的classpath中查找并加载所有可用的ApplicationListener
   - 设置main方法的定义类

3. 开始执行run方法的逻辑，首先遍历执行所有通过SpringFactoriesLoader加载到的SpringApplicationRunListener，调用它们的started()方法，告诉这些SpringApplicationRunListener，SpringBoot应用要开始执行了。

4. 创建并配置当前SpringBoot应用将要使用的Environment

5. 遍历并调用所有的SpringApplicationRunListener的environmentPrepared()方法，告诉它们，Springboot应用使用的Environment准备好了

6. 确定SpringBoot应用创建什么类型的ApplicationContext，并创建完成，然后根据条件决定是否使用自定义的ShutdownHook，是否使用自定义的BeanNameGenerator，是否使用自定义的ResourceLoader，然后将准备好的Environment设置给创建好的ApplicationContext使用

7. ApplicationContext创建完成，SpringApplication调用之前加载的ApplicationContextInitialize的initialize方法对创建好的ApplicationContext进行进一步的处理

8. 遍历所有SpringApplicationRunListener的contextPrepared()方法，通知它们，SpringBoot应用使用的ApplicationContext准备好了

9. 将之前通过@EnableAutoConfiguration获取的所有配置以及其他形式的Ioc容器配置加载到已经你准备完毕的ApplicationContext

10. 遍历所有的SpringApplicationRunListener的contextLoader()方法，告知ApplicationContext已装载完毕

11. 调用ApplicationContext的refresh()方法，完成Ioc容器可用的最后一道工序

12. 查找当前ApplicationContext中是否注册有CommandLineRunner，如果有，则遍历执行它们

13. 遍历所有的SpringApplicationRunListener的finished()方法，告知，“初始化完成”