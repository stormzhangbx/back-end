# Spring Bean 生命周期

[Spring Bean init-method 和 destroy-method实例](https://www.yiibai.com/spring/spring-init-method-and-destroy-method-example.html)
[Spring Bean InitializingBean和DisposableBean实例](https://www.yiibai.com/spring/spring-initializingbean-and-disposablebean-example.html)
[Spring @PostConstruct和@PreDestroy实例](https://www.yiibai.com/spring/spring-postconstruct-and-predestroy-example.html)

## 1 init-method 和 destroy-method参数

在基于 XML 的配置元数据的情况下，init-method 属性指定一个方法，在实例化 Bean 时，立即调用该方法。同样，destroy-method 指定一个方法，只有从容器中移除 Bean 之后，才能调用该方法。

即在XML配置文件中通过init-method、destroy-method属性指定初始化、销毁Bean时的回调。

这种情况仅适用于基于XML配置Spring时。

## 2 InitializingBean 和 DisposableBean接口

InitializingBean 和 DisposableBean是两个标记接口，定义了Bean初始化和销毁时回调动作。

- 对于实现了InitializingBean的Bean，初始化Bean时，将在所有Bean的属性被设置后，执行afterPropertiesSet方法
- 对于实现了DisposableBean的Bean，将在Spring容器释放该Bean后，执行destroy方法。

即通过实现接口，并重写其中方法来指定初始化、销毁Bean时的回调。

## 3 @PostConstruct 和 @PreDestroy

@PostConstruct 和 @PreDestroy都是用来注解方法。它们不属于 Spring，它是在J2EE库- common-annotations.jar。

综合例子

```java
import lombok.Getter;
import lombok.Setter;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import javax.annotation.PostConstruct;

@Service
@Getter
@Setter
public class HelloService implements InitializingBean {
    @Value("${hello.message}")
    private String message;

    public HelloService() {
        System.out.println("I am constructor");
        System.out.println(message);
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean#afterPropertiesSet: " + message);
    }

    @PostConstruct
    public void initIt() {
        System.out.println("@PostConstruct: " + message);
    }
}
```

启动项目，打印内容如下

```
I am constructor
null
@PostConstruct: hello world
InitializingBean#afterPropertiesSet: hello world
```

先实例化类，然后给属性赋值，在执行@PostConstruct注解的方法，最后执行afterPropertiesSet。

在@PostConstruct注解的方法和afterPropertiesSet中能打印出Bean属性message，说明这两个方法实在属性赋值完成后执行的。