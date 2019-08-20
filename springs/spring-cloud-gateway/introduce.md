# Introduce

## API

 API网关是一个服务器，是系统的唯一入口。从面向对象设计的角度看，它与外观模式类似。API网关封装了系统内部架构，为每个客户端提供一个定制的API。它可能还具有其它职责，如身份验证、监控、负载均衡、缓存、请求分片与管理、静态响应处理。  
API网关方式的核心要点是，所有的客户端和消费端都通过统一的网关接入微服务，在网关层处理所有的非业务功能。通常，网关也是提供REST/HTTP的访问API。服务端通过API-GW注册和管理服务。

### 工作原理

![](../../.gitbook/assets/springgateway.png)

![](../../.gitbook/assets/springgatewayworke.png)

## Gateway

网关服务是单一访问点，并充当多项服务的代理。服务网关启用了跨所有服务的路由转发、过滤和公共处理等。

在微服务实践中远不止这点功能，它可以做到统一接入、流量管控、安全防护、业务隔离等功能。

![GateWayFeatures](../../.gitbook/assets/gatewayfeatures.png)

![GatewayInSpringCloud](../../.gitbook/assets/gatewayinspringcloud.png)

### 单节点网关

![](../../.gitbook/assets/singlenoegateway.png)

### Backends for frontends 网关

![](../../.gitbook/assets/backendsforfrontends.png)

