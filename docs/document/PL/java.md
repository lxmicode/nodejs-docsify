# JAVA

## spring cloud
### Eureka注册中心
- pom.xml导入项目项目
- 启动类添加注解(@EnableEurekaServer)
```java
@SpringBootApplication
@EnableEurekaServer
public class SpringCloudEurekaApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringCloudEurekaApplication.class, args);
	}
}
```
- application.properties
```properties
#表示是否将自己注册到Eureka Server，默认为true
eureka.client.register-with-eureka=true
#表示是否从Eureka Server获取注册信息，默认为true
eureka.client.fetch-registry=true
#设置与Eureka Server交互的地址，查询服务和注册服务都需要依赖这个地址，多个逗号隔开
eureka.client.serviceUrl.defaultZone=http://localhost:8080/eureka/
#启动工程后，访问：http://localhost:8080，就可以看到Eureka接口
```

- 负载配置参考
```properties
#app1配置
eureka.client.serviceUrl.defaultZone=http://ip2:8080/eureka/
#app2配置
eureka.client.serviceUrl.defaultZone=http://ip1:8080/eureka/
#重点，defaultZone交互地址，填写除自己外的其它所有机器
```

### eureka服务提供者
- 启动类添加注解(@EnableDiscoveryClient),可以在注册中心的页面看到服务
```java
@SpringBootApplication
@EnableDiscoveryClient
public class SpringCloudEurekaApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringCloudEurekaApplication.class, args);
	}
}
```
- application.properties
```properties
spring.application.name=spring-cloud-producer
server.port=9001
eureka.client.serviceUrl.defaultZone=http://localhost:9001/eureka/
```
- 其它跟正常项目一致

### eureka服务调用
- 启动类添加注解(@EnableDiscoveryClient),可以在注册中心的页面看到服务
```java
@SpringBootApplication
#启用服务注册与发现
@EnableDiscoveryClient
#启用feign进行远程调用
@EnableFeignClients
```
- application.properties
```properties
spring.application.name=spring-cloud-consumer
eureka.client.serviceUrl.defaultZone=http://localhost:8080/eureka/
```
- 接口实现
```java
#name 远程服务名，及spring.application.name配置的名称
@FeignClient(name= "app_name")
public interface HelloRemote {
    @RequestMapping(value = "/hello")
    public String hello(@RequestParam(value = "name") String name);
}
```
- web调动
```java
@RestController
public class ConsumerController {
    @Autowired
    HelloRemote HelloRemote;
    
    @RequestMapping("/hello/{name}")
    public String index(@PathVariable("name") String name) {
        return HelloRemote.hello(name);
    }
}
```

## 参考
- [纯洁的微笑-学习系列](https://github.com/ityouknow/spring-cloud-examples)
