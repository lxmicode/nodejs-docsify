# JAVA

## spring cloud
### Eureka注册中心
- pom.xml导入jar
```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-eureka-server</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>
```
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
server.port=8000
#表示是否将自己注册到Eureka Server，默认为true
eureka.client.register-with-eureka=true
#表示是否从Eureka Server获取注册信息，默认为true
eureka.client.fetch-registry=true
#设置与Eureka Server交互的地址，查询服务和注册服务都需要依赖这个地址，多个逗号隔开
eureka.client.serviceUrl.defaultZone=http://localhost:${server.port}/eureka/
```
- 启动工程后，访问：http://localhost:8000，就可以看到Eureka接口   
[图片参考](http://favorites.ren/assets/images/2017/springcloud/eureka_start.jpg)


- 负载配置参考
```properties
#假设第一个应用app1配置
eureka.client.serviceUrl.defaultZone=http://peer1:8000/eureka/
#假设第二个应用app2配置
eureka.client.serviceUrl.defaultZone=http://ip1:8001/eureka/
#重点，defaultZone交互地址，填写除自己外的其它所有机器
#打包
mvn clean package
# 分别以peer1和peeer2 配置信息启动eureka
java -jar spring-cloud-eureka-0.0.1-SNAPSHOT.jar --spring.profiles.active=peer1
java -jar spring-cloud-eureka-0.0.1-SNAPSHOT.jar --spring.profiles.active=peer2
```
[图片参考](http://favorites.ren/assets/images/2017/springcloud/eureka-two.jpg)

### eureka服务提供
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
server.port=9000
#注册中心地址
eureka.client.serviceUrl.defaultZone=http://localhost:8000/eureka/
```
- 控制层 - 提供hello服务
```java
@RestController
public class HelloController {
	
    @RequestMapping("/hello")
    public String index(@RequestParam String name) {
        return "hello "+name+"，this is first messge";
    }
}
```
[图片参考](http://favorites.ren/assets/images/2017/springcloud/eureka_server.png)



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
server.port=9001
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
- 控制层调用
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

### 熔断器Hystrix
判断失败数次超过默认值，调用回调方法放回

- 配置 application.properties
```properties
#Feign中已经依赖了Hystrix所以在maven配置上不用做任何改动
feign.hystrix.enabled=true
```

- 创建回调类
```java
@Component
public class HelloRemoteHystrix implements HelloRemote{

    @Override
    public String hello(@RequestParam(value = "name") String name) {
        return "hello" +name+", this messge send failed ";
    }
}
```

- 添加fallback属性
```java
@FeignClient(name= "spring-cloud-producer",fallback = HelloRemoteHystrix.class)
public interface HelloRemote {

    @RequestMapping(value = "/hello")
    public String hello(@RequestParam(value = "name") String name);
}
```

- 测试
```txt
依次启动spring-cloud-eureka、spring-cloud-producer、spring-cloud-consumer三个项目。
浏览器中输入：http://localhost:9001/hello/neo
返回：hello neo，this is first messge
说明加入熔断相关信息后，不影响正常的访问。接下来我们手动停止spring-cloud-producer项目再次测试：
浏览器中输入：http://localhost:9001/hello/neo
返回：hello neo, this messge send failed
根据返回结果说明熔断成功。
```

### 熔断监控Hystrix Dashboard和Turbine

### 配置中心git示例
- 依赖
```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-config-server</artifactId>
	</dependency>
</dependencies>
```

- 配置文件
本地配置：spring.profiles.active=native，默认src/main/resource目录   
指定路径：spring.cloud.config.server.native.searchLocations=配置目录地址   
推荐使用git的方式。
```xml
server:
  port: 8001
spring:
  application:
    name: spring-cloud-config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/ityouknow/spring-cloud-starter/     # 配置git仓库的地址
          search-paths: config-repo                             # git仓库地址下的相对地址，可以配置多个，用,分割。
          username:                                             # git仓库的账号
          password:                                             # git仓库的密码
```

- 启动类
```java
@EnableConfigServer
@SpringBootApplication
public class ConfigServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigServerApplication.class, args);
	}
}
```

- 测试
直接访问：http://localhost:8001/neo-config/dev

### git refresh 刷新问题

- 依赖
```xml
<dependencies>
	<dependency>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter-actuator</artifactId>
	</dependency>
</dependencies>
```

- 开启更新机制
需要给加载变量的类上面加载@RefreshScope，在客户端执行/refresh
```java
@RestController
@RefreshScope // 使用该注解的类，会在接到SpringCloud配置中心配置刷新的时候，自动将新的配置更新到该类对应的字段中。
class HelloController {

    @Value("${neo.hello}")
    private String hello;

    @RequestMapping("/hello")
    public String from() {
        return this.hello;
    }
}
```

- 测试
以post请求的方式来访问http://localhost:8002/refresh 就会更新修改后的配置文件

## 参考
- [纯洁的微笑-学习系列](https://github.com/ityouknow/spring-cloud-examples)
