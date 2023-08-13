<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [十三、SpringBoot应用退出](#%E5%8D%81%E4%B8%89springboot%E5%BA%94%E7%94%A8%E9%80%80%E5%87%BA)
  - [异常退出](#%E5%BC%82%E5%B8%B8%E9%80%80%E5%87%BA)
    - [基于ExitCodeGenerator获取异常退出码](#%E5%9F%BA%E4%BA%8Eexitcodegenerator%E8%8E%B7%E5%8F%96%E5%BC%82%E5%B8%B8%E9%80%80%E5%87%BA%E7%A0%81)
    - [基于ExitCodeExceptionMapper Bean映射异常和退出码](#%E5%9F%BA%E4%BA%8Eexitcodeexceptionmapper-bean%E6%98%A0%E5%B0%84%E5%BC%82%E5%B8%B8%E5%92%8C%E9%80%80%E5%87%BA%E7%A0%81)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 十三、SpringBoot应用退出

![SpringBoot应用退出架构](./images/image-20221206185406326.png){width=50%}


## 异常退出
可以基于ExitCodeGenerator，也可以基于ExitCodeExceptionMapper Bean

### 基于ExitCodeGenerator获取异常退出码
```Java
static class ExitCodeGeneratorThrowable extends RuntimeException implements ExitCodeGenerator {
	public ExitCodeGeneratorThrowable(String message) {
		super(message);
	}

	@Override
	public int getExitCode() {
		return 95;
	}
}
```

### 基于ExitCodeExceptionMapper Bean映射异常和退出码

```Java
public interface ExitCodeExceptionMapper {
	int getExitCode(Throwable exception);
}

public class ExitCodeExceptionMapperBootstrap {

	@Bean
	public ExitCodeExceptionMapper exitCodeExceptionMapper() {
		return (throwable) -> 128;
	}
	
	public static void main(String[] args) {
		new SpringApplicationBuilder(ExitCodeExceptionMapperBootstrap.class)
		.listeners((ApplicationListener<ApplicationReadyEvent>) event -> {
			throw new RuntimeException();
		})
		.web(false)
		.run(args)
		.close();
	}
}
```

