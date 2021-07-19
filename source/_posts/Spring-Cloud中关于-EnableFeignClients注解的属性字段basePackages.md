---
title: Spring Cloud中关于@EnableFeignClients注解的属性字段basePackages
date: 2021-07-19 15:20:50
tags: 
  - spring-cloud
  - spring
---

  一般情况下，我们在启动类上面添加**@EnableFeignClients**注解就是表明当前应用服务（我们称之为`服务A`）中有的地方想要引用其它应用服务（我们称之为`服务B`）中的接口。
  如果`服务B可以单独启动起来并且注册到注册中心`，则我们仅仅在服务A的启动类中添加`@EnableFeignClients`注解即可；如果`服务B没有单独启动起来`，而是以Jar包的形式被引入到服务A中，则服务A在启动的时候是不会主动去加载服务B中标注了@FeignClient注解的interface而去自动生成bean对象，这样我们在服务A中的Controller类中以如下这种方式去引入`使用服务B中的标注了@FeignClient注解的接口`是会报错的

<!--more-->

```java
import com.bc.product.client.ProductClient;
 
@RestController
public class ClientController {
 
    @Autowired
    private ProductClient productClient;
 
    ...

```
这个时候就需要使用`basePackages`属性字段去指明应用程序A在启动的时候需要扫描服务B中的标注了@FeignClient注解的接口的包路径
```java

@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients(basePackages = {"com.bc.product.client"})
public class OrderApplication {
 
	public static void main(String[] args) {
		SpringApplication.run(OrderApplication.class, args);
	}
 
}

```