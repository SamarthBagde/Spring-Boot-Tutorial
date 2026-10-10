## 1.

Suppose we have a `Payment interface` with two implementations, `CardPayment` and `UpiPayment`. If `OrderService` requires a `Payment` dependency, which implementation will Spring inject? How can we control which implementation gets injected?


If both `CardPayment` and `UpiPayment` are registered as Spring Beans, Spring will find two candidates for the Payment dependency.

Without additional configuration, Spring generally throws a `NoUniqueBeanDefinitionException` because it cannot decide which implementation to inject.


### Example : 

Payment Interface 
```java
public interface Payment {
    void pay(double amount);
}
```

Two implementations of interface

```java
import org.springframework.stereotype.Component;

@Component
public class CardPayment implements Payment {
    @Override
    public void pay(double amount) {
        System.out.println("Paid by Card: " + amount);
    }
}
```

```java
import org.springframework.stereotype.Component;

@Component
public class UpiPayment implements Payment {
    @Override
    public void pay(double amount) {
        System.out.println("Paid by UPI: " + amount);
    }
}
```

OrderService

```java
import org.springframework.stereotype.Service;

@Service
public class OrderService {

    private final Payment payment;

    public OrderService(Payment payment) {
        this.payment = payment;
    }

    public void placeOrder() {
        payment.pay(1000);
    }
}
```


Here, Spring finds two beans implementing Payment, so it cannot choose one automatically unless you configure a preference.


### How do we decide which implementation gets injected?

There are three common approaches.

1. `@Primary`

    Use this when one implementation should be the default.
    ```java
    @Component
    @Primary
    public class UpiPayment implements Payment {
        @Override
        public void pay(double amount) {
            System.out.println("Paid by UPI");
        }
    }
    ```
    Spring injects `UpiPayment` into `OrderService` by default.

2. `@Qualifier`

    Use this when you want to explicitly select a particular bean.

    ```java
    @Component
    @Qualifier
    public class UpiPayment implements Payment {
        // Implementation
    }
    ```
    
    ```java
    @Component
    @Qualifier
    public class CardPayment implements Payment {
        // Implementation
    }
    ``` 

    ```java
    @Service
    public class OrderService {

        private final Payment payment;

        public OrderService(
            @Qualifier("cardPayment") Payment payment
        ) {
            this.payment = payment;
        }
    }
    ```
    Spring injects CardPayment, even if UpiPayment is marked @Primary

    Note : Bean name is same as class name but in camel case. Ex : cardPayment

3. `@Profile`

    Use this when different implementations should be registered in different environments or configurations.

    ```java
    @Component
    @Profile("development")
    public class UpiPayment implements Payment {
        // Implementation
    }
    ```
    
    ```java
    @Component
    @Profile("production")
    public class CardPayment implements Payment {
        // Implementation
    }
    ``` 