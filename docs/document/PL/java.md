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
#重点，defaultZone交互地址，填写除自己的其它所有机器
```

### eureka服务提供与调用
- 启动类添加注解(@EnableDiscoveryClient)
```java
@SpringBootApplication
@EnableDiscoveryClient
public class SpringCloudEurekaApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringCloudEurekaApplication.class, args);
	}
}
```

##参考
- [纯洁的微笑-学习系列](https://github.com/ityouknow/spring-cloud-examples)
