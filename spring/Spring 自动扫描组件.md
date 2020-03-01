# Spring自动扫描组件

## 1 手动声明组件

定义两个bean

```java
package com.company.customer.dao;

public class CustomerDAO
{
	@Override
	public String toString() {
		return "Hello , This is CustomerDAO";
	}
}
```

```java
package com.company.customer.services;

import com.company.customer.dao.CustomerDAO;

public class CustomerService
{
	CustomerDAO customerDAO;

	public void setCustomerDAO(CustomerDAO customerDAO) {
		this.customerDAO = customerDAO;
	}

	@Override
	public String toString() {
		return "CustomerService [customerDAO=" + customerDAO + "]";
	}

}
```

bean配置文件（beans.xml）

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">

	<bean id="customerService" class="com.company.customer.services.CustomerService">
		<property name="customerDAO" ref="customerDAO" />
	</bean>

	<bean id="customerDAO" class="com.company.customer.dao.CustomerDAO" />

</beans>
```

执行程序

```java
package com.company.common;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.company.customer.services.CustomerService;

public class App
{
    public static void main( String[] args )
    {
    	ApplicationContext context =
    	  new ClassPathXmlApplicationContext("beans.xml");

    	CustomerService cust = (CustomerService)context.getBean("customerService");
    	System.out.println(cust);

    }
}
```

输出结果 `CustomerService [customerDAO=Hello , This is CustomerDAO]`

## 2 自动扫描组件

使用@Component注释来表示这是类是一个自动扫描组件。

```java
package com.company.customer.dao;

import org.springframework.stereotype.Component;

@Component
public class CustomerDAO
{
	@Override
	public String toString() {
		return "Hello , This is CustomerDAO";
	}
}
```

```java
package com.company.customer.services;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.company.customer.dao.CustomerDAO;

@Component
public class CustomerService
{
	@Autowired
	CustomerDAO customerDAO;

	@Override
	public String toString() {
		return "CustomerService [customerDAO=" + customerDAO + "]";
	}
}
```

添加context:component-scan，意味者在Spring中启用自动扫描功能。base-package 是指明存储组件，Spring将扫描该文件夹，并找出Bean(注解为@Component)并注册到 Spring 容器。

```java
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context-2.5.xsd">

	<context:component-scan base-package="com.company.customer" />

</beans>
```

执行程序

```java
package com.company.common;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.company.customer.services.CustomerService;

public class App
{
    public static void main( String[] args )
    {
    	ApplicationContext context =
    	   new ClassPathXmlApplicationContext("beans.xml");

    	CustomerService cust = (CustomerService)context.getBean("customerService");
    	System.out.println(cust);

    }
}
```

输出结果 `CustomerService [customerDAO=Hello , This is CustomerDAO]`

## 3 自定义自动扫描组件名称

默认情况下，Spring 将第一个字符小写的类名作为bean名字，要创建自定义的bean名字，可以这样定义：

```java
@Service("AAA")
public class CustomerService
...
```

现在，可以用'AAA'这个名称进行检索。

```java
CustomerService cust = (CustomerService)context.getBean("AAA");
```

## 4 自动组件扫描注释类型

在Spring2.5中，有4种类型的组件自动扫描注释类型

- @Component – 指示自动扫描组件。
- @Repository – 表示在持久层DAO组件。
- @Service – 表示在业务层服务组件。
- @Controller – 表示在表示层控制器组件。

查看@Repository、@Service、@Controller的源码可以发现，它们都被@Component注解。上述注解的功能都是一样的，只是语义不同。为了便于阅读，不同的环境应该使用不同的注解，所以上述代码可以改为

DAO 层

```java
package com.company.customer.dao;

import org.springframework.stereotype.Repository;

@Repository
public class CustomerDAO
{
	@Override
	public String toString() {
		return "Hello , This is CustomerDAO";
	}
}
```

Service 层

```java
package com.company.customer.services;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.company.customer.dao.CustomerDAO;

@Service
public class CustomerService
{
	@Autowired
	CustomerDAO customerDAO;

	@Override
	public String toString() {
		return "CustomerService [customerDAO=" + customerDAO + "]";
	}

}
```

## 5 Spring Boot 中自动扫描组件

Spring Boot 同样可以使用@Component、@Repository、@Service、@Controller注解将bean组件纳入到bean容器中管理。此时不需要`<context:component-scan base-package="com.xxx.xxx" />`，只需保证启动Spring Boot 的main入口在最上层包就行。

