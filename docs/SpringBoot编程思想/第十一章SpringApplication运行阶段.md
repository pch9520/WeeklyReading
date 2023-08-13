<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [十一、SpringApplication运行阶段](#%E5%8D%81%E4%B8%80springapplication%E8%BF%90%E8%A1%8C%E9%98%B6%E6%AE%B5)
  - [准备过程](#%E5%87%86%E5%A4%87%E8%BF%87%E7%A8%8B)
  - [SpringApplicationRunListener](#springapplicationrunlistener)
    - [自定义SpringApplicationRunListener](#%E8%87%AA%E5%AE%9A%E4%B9%89springapplicationrunlistener)
    - [Spring Boot事件](#spring-boot%E4%BA%8B%E4%BB%B6)
  - [Spring应用上下文启动阶段](#spring%E5%BA%94%E7%94%A8%E4%B8%8A%E4%B8%8B%E6%96%87%E5%90%AF%E5%8A%A8%E9%98%B6%E6%AE%B5)
  - [Spring应用上下文启动后阶段](#spring%E5%BA%94%E7%94%A8%E4%B8%8A%E4%B8%8B%E6%96%87%E5%90%AF%E5%8A%A8%E5%90%8E%E9%98%B6%E6%AE%B5)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 十一、SpringApplication运行阶段
## 准备过程
准备过程的核心对象有：
1. ApplicationArguments;
2. SpringApplicationRunlisteners;
3. ConfigurableEnvironment;
4. Banner;
5. ConfigurableApplicationContext;
6. SpringBootExceptionReporter.

## SpringApplicationRunListener

SpringApplicationRunListener 接口的作用主要就是在Spring Boot 启动初始化的过程中可以通过SpringApplicationRunListener接口回调来让用户在启动的各个流程中可以加入自己的逻辑。  
Spring Boot启动过程的关键事件（按照触发顺序）包括：

1.  开始启动；
2.  Environment构建完成；
3.  ApplicationContext构建完成；
4.  ApplicationContext完成加载；
5.  ApplicationContext完成刷新并启动；
6.  启动完成；
7.  启动失败；

### 自定义SpringApplicationRunListener
我们可以通过实现自定义SpringApplicationRunListener来监听Spring Boot的启动流程，并在各个回调方法中处理自己的逻辑。  
实现自定义SpringApplicationRunListener需要实现SpringApplicationRunListener接口，并且必须提供一个包含参数（SpringApplication application, String[] args）的构造方法，如下所示：

```Java
public class HelloApplicationRunListener implements SpringApplicationRunListener {
    public HelloApplicationRunListener(SpringApplication application, String[]  args){
        System.out.println("constructor");
    }

    @Override
    public void starting() {
        System.out.println("starting...");
    }

    @Override
    public void environmentPrepared(ConfigurableEnvironment environment) {
        System.out.println("environmentPrepared...");
    }

    @Override
    public void contextPrepared(ConfigurableApplicationContext context) {
        System.out.println("contextPrepared...");
    }

    @Override
    public void contextLoaded(ConfigurableApplicationContext context) {
        System.out.println("contextLoaded...");
    }

    @Override
    public void started(ConfigurableApplicationContext context) {
        System.out.println("started...");
    }

    @Override
    public void running(ConfigurableApplicationContext context) {
        System.out.println("running...");
    }

    @Override
    public void failed(ConfigurableApplicationContext context, Throwable exception) {
        System.out.println("failed...");
    }
}
```

在spring.factories中配置自定义SpringApplicationRunListener，之后在启动Spring Boot应用时，就会自动将HelloApplicationRunListener实例化，并在Spring容器初始化的各个阶段回调对应的方法。
```Java
org.springframework.boot.SpringApplicationRunListener=\
 com.yidian.data.listener.HelloApplicationRunListener
```

### Spring Boot事件
主要分三步：
1. 定义事件；
2. 监听事件；
3. 事件发布。
**事件定义：**
![事件定义](./images/Pasted%20image%2020221127193050.png){width=50%}
```Java
// 定义一个事件
public class MyEvent extends ApplicationEvent {
    private String message;
    public EventDemo(Object source, String message) {
        super(source);
        this.message = message;
    }
    public String getMessage() {
        return message;
    }
}
```

**监听事件：**
```Java
// 定义一个事件监听者
@Component
public class MyEventListener implements ApplicationListener<MyEvent> {
    @Override
    public void onApplicationEvent(MyEvent event) {
        // 这里写事件处理逻辑
    }
}
```

也可以使用注解`@EventListener`：
```Java
// 定义一个事件监听者
@Component
public class MyEventListener  {
    @EventListener
    public void onApplicationEvent(MyEvent event) {
        // 这里写事件处理逻辑
    }
}
```

**事件发布：**
```Java
@Autowired
ApplicationEventPublisher applicationEventPublisher;

public void publishEvent(){
    MyEvent event = new MyEvent(this);
    applicationEventPublisher.publishEvent(event);
}
```

## Spring应用上下文启动阶段
通过SpringApplication#refreshContext接口实现
```Java
public class SpringApplication {

	protected void refresh(ApplicationContext applicationContext) {
		Assert.isInstanceOf(AbstractApplicationContext.class, applicationContext);
		// ApplicationContext refresh接口是protected
		// 且只在AbstractApplicationContext的接口(子接口)中进行了实现
		// 因此说设计得不太好
		((AbstractApplicationContext) applicationContext).refresh();
	}
	...

	private void refreshContext(ConfigurableApplicationContext context) {
		refresh(context);
		if (this.registerShutdownHook) {
			try {
				context.registerShutdownHook();
			} catch (AccessControlException ex) {
				// Not allowed in some environments.
			}
		}
	}
}
```


## Spring应用上下文启动后阶段
触发ApplicationStartedEvent，此时context已经加载，因此该事件可以获取所依赖的Spring Bean
```Java
public class SpringApplication {
	...
	public ConfigurationApplicationContext run(String... args) {
		...
		try {
			...
			afterRefresh(context, applicationArguments);
			...
			// 事件监听器，监听了SpringApplication启动后事件
			listeners.started(context);
			callRunners(context, applicationArguments);
		}
		...
		return context;
	}
	...
}
```
