<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [九、Spring Boot自动装配](#%E4%B9%9Dspring-boot%E8%87%AA%E5%8A%A8%E8%A3%85%E9%85%8D)
  - [Spring Boot自动装配原理](#spring-boot%E8%87%AA%E5%8A%A8%E8%A3%85%E9%85%8D%E5%8E%9F%E7%90%86)
    - [步骤：](#%E6%AD%A5%E9%AA%A4)
    - [@EnableAutoConfiguration排序自动装配组件](#enableautoconfiguration%E6%8E%92%E5%BA%8F%E8%87%AA%E5%8A%A8%E8%A3%85%E9%85%8D%E7%BB%84%E4%BB%B6)
    - [Spring Boot条件化自动装配](#spring-boot%E6%9D%A1%E4%BB%B6%E5%8C%96%E8%87%AA%E5%8A%A8%E8%A3%85%E9%85%8D)
    - [属性条件注解](#%E5%B1%9E%E6%80%A7%E6%9D%A1%E4%BB%B6%E6%B3%A8%E8%A7%A3)
    - [Resource条件注解](#resource%E6%9D%A1%E4%BB%B6%E6%B3%A8%E8%A7%A3)
    - [Spring表达式条件注解](#spring%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%9D%A1%E4%BB%B6%E6%B3%A8%E8%A7%A3)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 九、Spring Boot自动装配
## Spring Boot自动装配原理
### 步骤：
1. 加载自动装配元信息；
2. 获取@EnableAutoConfiguration标注类的元信息；
3. 获取自动装配的候选类名集合；
4. 移除重复的自动装配配置；
5. 排除exclusions相关的配置；
6. 以第一步中获取的自动装配元信息对配置进行过滤，过滤自动装配Class集合中Class不存在的成员；
7. 触发一个自动装配的导入事件，该事件可能包括候选的装配组件类名单和排除名单。

上面第一步中如何获取自动装配元信息？
1）搜索指定ClassLoader下所有的META-INF/spring.factories资源内容；
2）将一个或多个META-INF/spring.facotries资源内容作为Properties文件读取，合并为一个Key为接口的全类名，Value是实现类全类名列表的Map，作为loadSpringFactories(ClassLoader)方法返回值；
3）再从上一步返回的Map中查找并返回方法指定类名所映射的实现类全类名列表。

如何移除重复的自动装配配置？
将标注@EnableAutoConfiguration配置类的注解属性（AnnotationAttributes）exclude和excludeName以及将spring.autoconfigure.exclude配置值累加至排除集合excluded。

### @EnableAutoConfiguration排序自动装配组件
SpringBoot提供两种自动装配组件的排序手段：
1. 绝对自动装配顺序——@AutoCOnfigurationOrder；
2. 相对自动装配顺序——@AutoConfigurationBefore和@AutoConfigurationAfter。

排序方式一般先按字母进行排序，再按照AutoConfigurationOrder进行排序，最后再进入@AutoConfigurationBefore和@AutoConfigurationAfter排序过程

### Spring Boot条件化自动装配
利用@ConditionalOnClass和@ConditionalOnMissingClass装配来判断装配哪个Bean
```Java
@ConditionalOnClass(value="x")
@Bean
public xxxx yyy() {
	return new xxxx();
}
当x.class类存在时，返回xxxx Bean

@ConditionalOnMissingClass(value="y")
@Bean
public zzzz defaultyyy() {
	return new zzzz();
}
当y.class不存在时，返回zzzz Bean
```

另外其他条件注释：
@ConditionalOnBean（仅仅在当前上下文中存在某个对象时，才会实例化一个Bean）
@ConditionalOnExpression（当表达式为true的时候，才会实例化一个Bean）
@ConditionalOnMissingBean（仅仅在当前上下文中不存在某个对象时，才会实例化一个Bean）
@ConditionalOnNotWebApplication（不是web应用）

### 属性条件注解

@ConditionalOnProperty
- prefix()  配置属性名称前缀；
- value()  name()的别名，参考name()说明；
- name()  如果prefix()不为空，则完整配置属性名称为prifix() + name()，否则为name()的内容；
- havingValue()  表示期望的配置属性值，并且禁止使用false；
- matchIfMissing()  用于判断当属性值不存在时是否匹配。

### Resource条件注解
`@ConditionalOnResource`注释仅允许在存在特定资源时执行自动配置。 可以使用常用的 Spring 约定来指定资源，如以下示例所示：file：/home/user/test.dat。

### Spring表达式条件注解
效果跟`@ConditionalOnProperty`是差不多的
一个例子：
```Java
@Configuration
@ConditionalOnExpression("${spring.aop.auto:true}")
// @ConditionalOnProperty用法
@ConditionalOnPeoperty(prefix="spring.aop", name="auto", havingValue="true", matchIfMissing=true)
public class AopAutoConfiguration {
	....
}
```




