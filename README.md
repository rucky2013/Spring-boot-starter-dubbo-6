# Spring-boot-starter-dubbo
## 这是什么
Spring-boot是一个全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。

Spring-boot-starters是一组可以让你在构建应用时使用的方便的依赖描述符。对于某个特定技术方案它提供相对一站式的配置从而让你免去从一大堆示例中拷贝粘贴重复配置的困扰。 

## 有什么用
这是一个[Dubbo](http://dubbo.io/)的starter，意味着你可以轻易在一个轻量级的Spring-Boot容器中获取分布式调用的能力，而不需要再创建任何的xml文件，从而加速你构建微服务和业务迭代的进程。如果你还不知道该如何使用Dubbo的话，可以先看下[这里](http://dubbo.io/User+Guide-zh.htm)。

## 如何使用

### 1. 构建和安装


	git clone http://git.movit-tech.com/Andy.Li/Spring-boot-starter-dubbo.git
	cd Spring-boot-starter-dubbo
	mvn install

Spring-boot-starter-dubbo就被添加到本地的Maven仓库里了。

### 2. 添加依赖
首先你要有一个Spring-Boot工程，不知道怎么做的话操作[这里](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/)。然后把本工程构建的依赖添加进去，

	<dependency>
	     <groupId>org.springframework.boot</groupId>
	     <artifactId>spring-boot-starter-dubbo</artifactId>
	     <version>1.0-SNAPSHOT</version>
	 </dependency>

### 3. 发布服务

服务接口:

	package app;

	public interface AppService {
	    String test(String str);
	}


在application.properties添加Dubbo信息如下：

	spring.dubbo.application.name=provider
	spring.dubbo.registry.address=zookeeper://127.0.0.1:2181
	spring.dubbo.protocol.name=dubbo
	spring.dubbo.protocol.port=20880
	spring.dubbo.scan=app


在需要发布的服务实现上添加@Service注解：

	package app;

	import com.alibaba.dubbo.config.annotation.Service;

	@Service(version = "1.0.0")
	public class AppServiceImpl implements AppService {	
	    public String test(String str) {
	        return str.toUpperCase();
	    }
	}

### 4. 调用服务
统一在application.properties添加Dubbo配置信息：

	spring.dubbo.application.name=consumer
	spring.dubbo.registry.address=zookeeper://127.0.0.1:2181
	spring.dubbo.scan=app

在需要调用的服务接口上添加@Reference注解：
	
	package app;
	
	import com.alibaba.dubbo.config.annotation.Reference;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class AppController {
    
        @Reference(version = "1.0.0")
        private AppService appService;
    
        @RequestMapping("/api/{input}")
        public String echo(@PathVariable String input) {
            return appService.test(input);
        }
    }

分别启动服务提供者和消费者就可以了。