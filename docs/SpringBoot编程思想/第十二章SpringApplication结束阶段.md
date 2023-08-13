<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [十二、SpringApplication结束阶段](#%E5%8D%81%E4%BA%8Cspringapplication%E7%BB%93%E6%9D%9F%E9%98%B6%E6%AE%B5)
  - [异常退出处理步骤](#%E5%BC%82%E5%B8%B8%E9%80%80%E5%87%BA%E5%A4%84%E7%90%86%E6%AD%A5%E9%AA%A4)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 十二、SpringApplication结束阶段

SpringApplication结束时执行下面方法：
`SpringApplicationRunListener#finished(ConfigurableApplicationContext, Throwable)`
该方法涵盖了正常结束和异常结束的语义。

## 异常退出处理步骤

![异常退出处理步骤](./images/Pasted%20image%2020221201015447.png){width=50%}