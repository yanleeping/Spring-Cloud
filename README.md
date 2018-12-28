什么是“微服务架构”呢？
简单的说，微服务架构就是将一个完整的应用从数据存储开始垂直拆分成多个不同的服务,每个服务都能独立部署、独立维护、独立扩展，服务与服务间通过诸如RESTful API的方式互相调用。
    
Spring Cloud是一个基于Spring Boot实现的云应用开发工具，它为基于JVM的云应用开发中的，它为配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话、集群状态管理等操作提供了一种简单的开发方式。

Spring Cloud包含了多个子项目（针对分布式系统中涉及的多个不同开源产品），比如：
  Spring Cloud Netflix 是 Spring Cloud 的子项目之一，主要内容是对Netflix公司一系列开源产品的包装。
  它为Spring Boot应用提供了自配置的Netflix OSS整合。
  通过一些简单的注解，开发者就可以快速的在应用中配置一下常用模块并构建庞大的分布式系统。
  它主要提供的模块包括：服务发现（Eureka），断路器（Hystrix），智能路有（Zuul），客户端负载均衡（Ribbon）等。
   
SpringBoot和SpringCloud的版本必须对应，本示版本是：SpringBoot-2.1.0.RELEASE + SpringCloud-Greenwich.M3
1.建立服务注册中心Eureka-Server：作为服务注册与发现的组件，当有服务注册到注册中心将会被发现、
  @EnableEurekaServer —— 启动一个服务注册中心提供给其他应用进行对话  
  Eureka Server 实现高可用-多个Eureka-Server集群  
  当client向server注册后，Eureka server 从每个client实例接收心跳消息。如果心跳超时，则通常将该实例从注册server中删除。
  
2.建立注册中心客户端Eureka-Client：向服务中心注册服务提供者和服务消费者，他们都将作为Eureka-Client被Eureka-Server检测到。
  开启Eureka客户端的注解有 @EnableEurekaClient 与 @EnableDiscoveryClientuy 区别如下
  @EnableEurekaClient    注解是基于spring-cloud-netflix 依赖，只能为eureka作用、
  @EnableDiscoveryClient 注解是基于spring-cloud-commons 依赖，并且在classpath 中实现、
  SpringCLoud 中的 “Discovery Service” 有多种实现，比如：eureka, consul, zookeeper     

3.Ribbon 负载均衡客户端
  Ribbon 是一个基于 HTTP 和 TCP 的客户端负载均衡器，RestTemplate 用来发起 REST请求。
  @Bean 初始化 RestTemplate 后，开启@LoadBalanced注解，就实现了RestTemplate结合Ribbon开启负载均衡功能。
  Spring Cloud Ribbon 有个拦截器，在实际调用时自动选取服务实例，并将服务名替换成实际请求的IP地址和端口，来完成服务接口的调用。
	@Bean          
	@LoadBalanced  
	RestTemplate restTemplate() {
		return new RestTemplate();
	}
  
4.Hystrix 断路器，是Netflix开源的微服务框架套件之一，它本身是一种开关装置，预防服务雪崩（防止程序出错导致网络阻塞）
  Hystrix 框架目标在于通过控制那些访问远程系统、服务和第三方库的节点，从而对延迟和故障提供更强大的容错能力。
	Hystrix 具备拥有回退机制和断路器功能的线程和信号隔离，请求缓存和请求打包，以及监控和配置等功能。
  分布式系统中经常会出现因服务提供者/消费者不可用，并将不可用逐渐放大，造成整个系统不可用，这种现象被称为服务雪崩效应。    
  @EnableHystrixDashboard 是监控单个服务实例的Hystrix熔断的各项度量指标展示组件
  @EnableTurbine 高可用的聚合服务的监控数据界面组件  

5.Feign 包含了Ribbon负载均衡，也包含Hystrix服务容错
  @EnableFeignClients     —— 标示开启Feign客户端功能，并对@FeignConfig注解的包的扫描。
  @EnableCircuitBreaker   —— 开启断路器模式的注解，当请求超时或服务提供者停掉时，请求会走短路模式设定的方法
  @EnableHystrixDashboard —— 监控Hystrix的熔断器的一个组件，提供了数据监控和友好的展示界面
  
  @SpringCloudApplication包含了注解：@SpringBootApplication、@EnableDiscoveryClient、@EnableCircuitBreaker
  SpringCloud 搭建微服务必须应有的三个注解，所以才推出了@SpringCloudApplication包含了注解。
  
6.Zuul 网关服务——路由器功能 
  Zuul是 Netflix 基于JVM 的路由器和服务器端负载均衡器。Zuul的核心是反向代理。
  Zuul、Ribbon以及Eureka结合可以实现智能路由和负载均衡的功能。
  网关将所有服务的API接口统一聚合，统一对外暴露。外界调用API接口时，不需要知道微服务系统中各服务相互调用的复杂性，保护了内部微服务单元的API接口；
	网关可以做用户身份认证和权限认证，防止非法请求操作API接口；
	网关可以实现监控功能，实时日志输出，对请求进行记录；
	网关可以实现流量监控，在高流量的情况下，对服务降级；    
  @EnableZuulProxy 启用zuul代理，让请求转发到适当的服务，整合了@EnableCircuitBreaker、@EnableDiscoveryClient目的是简化配置。

7.Turbine 集群监控 

8.Springcloud-config 远程获取配置，它分为服务端与客户端两个部分。
  服务端：提供配置文件的本地|远端存储，再将配置文件发布成REST接口提供出去。
  客户端：通过服务端提供出来的REST接口去获取配置文件。
  @EnableConfigServer来标识Config-Server端
	Config-Server实现高可用-将Server加入Eureka的服务治理体系中。
  —— Spring Cloud Config 集成git（默认使用git方式，推荐git方式,更好的管理内容和版本控制）
  —— Spring Cloud Config 集成svn来进管控  
  spring.profiles.active=subversion  使用svn方式必须指定配置中心使用svn，否则还是使用的git方式。   
  
9.Spring Cloud Bus 将分布式的节点用轻量的消息代理连接起来管理和传播，集成RabbitMQ或Kafka来建立一个多个应用之间的通信频道。
  Spring Cloud Bus 利用了(消息队列的发布订阅模型)的广播机制在分布式的系统中传播消息。
  Spring Cloud Bus + RabbitMQ = 消息总线
    
  可以在Server或Client端去配置消息总线的支持，在Client端配置的方式不够优雅，推荐在Server端配置。
   
  —>在Client端增加对消息总线的支持配置RebbitMq，那么该客户端就具备了消息总线通知的能力，步骤如下：
    1、客户端提交代码，触发post给客户端A发送/actuator/bus-refresh
    2、客户端A接收到请求从Server端更新配置并且发送给Spring Cloud Bus
    3、Spring Cloud bus接到消息并通知给其它客户端
    4、其它客户端接收到通知，请求Server端获取最新配置
    5、全部客户端均获取到最新的配置 
   
   —>以上方式不够优雅，原因如下：
   a.打破了微服务的职责单一性。微服务本身是业务模块，它本不应该承担配置刷新的职责。
   b.破坏了微服务各节点的对等性。
   c.有一定的局限性。例如微服务在迁移时，它的网络地址常会发生变化，此时如果想要做到自动刷新，那就不得不修改WebHook的配置。
   
   —>改在Server端引入消息总线支持，配置RebbitMq，那么服务端就具备了消息总线通知的能力，步骤如下：   
    1、提交代码触发post请求给/actuator/bus-refresh
    2、server端接收到请求并发送给Spring Cloud Bus
    3、Spring Cloud bus接到消息并通知给其它客户端
    4、其它客户端接收到通知，请求Server端获取最新配置
    5、全部客户端均获取到最新的配置
 
 
