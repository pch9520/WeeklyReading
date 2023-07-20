# 十二、SpringApplication结束阶段

SpringApplication结束时执行下面方法：
`SpringApplicationRunListener#finished(ConfigurableApplicationContext, Throwable)`
该方法涵盖了正常结束和异常结束的语义。

## 异常退出处理步骤

![异常退出处理步骤](./images/Pasted%20image%2020221201015447.png){width=50%}