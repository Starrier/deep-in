# Filter

 Spring Gateway支持两种方式提供路由服务，其一是配置文件启用，其二则是通过代码达到目的

 Gateway默认转发是全路径的，设置`StripPrefix=1`表示从二级url路径转发，即`http://localhost:port/activity/test`将会转发到`http://{activity}/test`

