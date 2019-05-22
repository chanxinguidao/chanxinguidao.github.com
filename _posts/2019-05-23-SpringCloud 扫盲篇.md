# SpringCloud 扫盲篇

### 开篇

学习springcloud的前提我已经认为你已经具备：

1. 微服务的基本概念
2. 具备springboot的基本用法



![](C:\Users\69319\Desktop\20180322093134220.jpg)

eurake server:注册中心,对标zookeeper
eurake client:服务,对标dubbo
ribbon:负载均衡,对标nginx
feign:与ribbon类似,目前项目没有使用,暂时就不写
hystrix:断路器,目前没有使用。当对特定的服务的调用的不可用达到一个阀值（Hystric 是5秒20次） 断路器将会被打开

zuul:项目用来验证token,zuul常见使用场景

