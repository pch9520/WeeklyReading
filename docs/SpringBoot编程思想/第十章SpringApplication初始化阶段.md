<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [十、SpringApplication初始化阶段](#%E5%8D%81springapplication%E5%88%9D%E5%A7%8B%E5%8C%96%E9%98%B6%E6%AE%B5)
  - [SpringApplication构造阶段](#springapplication%E6%9E%84%E9%80%A0%E9%98%B6%E6%AE%B5)
  - [SpringApplication配置阶段](#springapplication%E9%85%8D%E7%BD%AE%E9%98%B6%E6%AE%B5)
    - [调整SpringApplication设置](#%E8%B0%83%E6%95%B4springapplication%E8%AE%BE%E7%BD%AE)
    - [增加SpringApplication配置源](#%E5%A2%9E%E5%8A%A0springapplication%E9%85%8D%E7%BD%AE%E6%BA%90)
    - [调整Spring Boot外部化配置](#%E8%B0%83%E6%95%B4spring-boot%E5%A4%96%E9%83%A8%E5%8C%96%E9%85%8D%E7%BD%AE)
  - [附录](#%E9%99%84%E5%BD%95)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 十、SpringApplication初始化阶段
SpringApplication的准备阶段由两阶段完成：
1. 构造阶段；
2. 配置阶段

## SpringApplication构造阶段
1. 推断Web应用类型（在SpringApplication类实例初始化阶段运行`deduceMainApplicationClass()`)；
2. 加载Spring应用上下文初始化器；
如何加载Spring应用上下文初始化器？

![SpringApplication构造阶段](./images/image-20221206185502805.png){width=50%}

用跟上述类似的方式加载Spring应用事件监听器(ApplicationListener)
最后进行推断应用引导类，即推断main方法所在的类

## SpringApplication配置阶段
### 调整SpringApplication设置
调用`app.setXXXXX()`方法调整设置，如
```Java
public static void main(String[] args) {
  SpringApplication app = new SpringApplication(MySpringConfiguration.class);
  app.setBannerMode(Banner.Mode.OFF);
  app.run(args);
}
```

### 增加SpringApplication配置源
SpringApplication配置源：
1. 主配置类名称；
2. @Configuration Class名称;
3. XML配置文件和package名称;
通过SpringApplication#setSources接口来完成
```Java
public class SpringApplication {
  public void setSources(Set<String> sources) {
    Assert.notNull(sources, "Sources must not be null");
    this.sources = new LinkedHashSet<>(sources);
  }
}
```

###  调整Spring Boot外部化配置
如application.properties文件，就是外部化配置

## 附录
Java异常层次结构

![Java异常层次结构](./images/Pasted%20image%2020221118010451.png){width=50%}