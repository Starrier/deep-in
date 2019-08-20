# SpringBoot 启动过程

可以对 SpringApplication 执行流程进行干预来扩展完成对流程的扩展。

![SpringBoot-run\(\)](../../.gitbook/assets/springbootrun.png)

1. 通过 SpringFactoriesLoader 加载 META-INF/spring.factories 文件，获取并创建 SpringApplicationRunListener 对象。
2. 然后 SpringApplicatinRunListener 来发出 starting 消息
3. 创建参数，并配置当前 SpringBoot 应用将要使用的 Enviroment
4. 完成之后，依然由 SpringApplicationRunListener 来发出 environmentPrepared 消息
5. 创建 ApplicationContext
6. 初始化 ApplicationContext，并设置 Environment，加载相关配置等。
7. 由 SpringApplicationRunListener 来发出 contextPrepared 消息，告知 SpringBoot 应用使用的 ApplicationContext 已经准备好。
8. 将各种 beans 装载入 ApplicationContext，继续由 SpringApplicationRunListener 来发出 contextLoaded 消息，告诉 SpringBoot 应用使用的 ApplicationContext 已经填装好。
9. refresh ApplicationContext，完成 IoC 容器可用的最后一步。
10. 由 SpringApplicationRunListener 来发出 started 消息。
11. 完成最终的程序启动。
12. 由 SpringApplicationRunListener 来发出 running 消息，告诉程序已经运行起来了。

