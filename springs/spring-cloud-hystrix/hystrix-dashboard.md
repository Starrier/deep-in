# Hystrix Dashboard

 Hystrix Dashboard 共支持三种不同的监控方式：



* 默认的集群监控：通过 URL：http://turbine-hostname:port/turbine.stream 开启，实现对默认集群的监控。
* 指定的集群监控：通过 URL：http://turbine-hostname:port/turbine.stream?cluster=\[clusterName\] 开启，实现对 clusterName 集群的监控。
* 单体应用的监控： ~~通过 URL：http://hystrix-app:port/hystrix.stream 开启~~ ，实现对具体某个服务实例的监控。**（现在这里的 URL 应该为 http://hystrix-app:port/actuator/hystrix.stream，Actuator 2.x 以后 endpoints 全部在`/actuator`下，可以通过`management.endpoints.web.base-path`修改）**

