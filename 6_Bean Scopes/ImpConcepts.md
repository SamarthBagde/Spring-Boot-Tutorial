## 1. Singleton Scope working 

Suppose 

Main.java
```java
package in.strikes;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.core.annotation.Order;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        OrderService order = context.getBean(OrderService.class);

        System.out.println(order == order2);
    }
}
```

OrderService.java

```java
package in.strikes;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

//@Component
@Scope("singleton")  
public class OrderService {

    public OrderService() {
        System.out.println("OrderService created");
    }

    public void placeOrder() {
        System.out.println("Order placed");
    }
}
```

AppConfig.java

```java
package in.strikes;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class AppConfig {

    @Bean
    public OrderService getOrder() {
        return new OrderService();
    }

    @Bean
    public OrderService getOrder2() {
        return new OrderService();
    }
}
```

Here Spring creates two different bean definatons :

```
getOrder
getOrder2
```

Both are singleton by default

So spring creates:

```
getOrder -> one Order object
getOrder2 -> one Order object
```

Total 

```
2 bean definations
2 singletone objects
```

Therfore :

In Spring , singleton means one object per bean name or bean defination inside the spring container

It does not mean one object per class.