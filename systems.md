# 11장 시스템

## 시스템 제작과 시스템 사용을 분리하라
소프트웨어 시스템에서 **객체를 생성하는 과정(설정, 초기화)** 과 **실제 사용(비즈니스 로직 실행)** 을 분리하는 것은 중요한 설계 원칙이다.

이것을 도시 건설에 비유하여 설명할 수 있다.
- 건물을 지을 때는 **설계도** 가 필요하며, **건축팀** 이 건물의 기초부터 창문 하나까지 조립한다.  
- 하지만 건물이 완성된 후에는 **건축팀이 사라지고**, 사람들이 건물을 사용한다.  

소프트웨어도 이와 마찬가지로, **객체를 생성하는 과정과 실제 실행 로직을 분리** 하는 것이 바람직하다.  
이러한 분리를 통해 얻을 수 있는 장점은 다음과 같다.
- **코드의 가독성이 향상된다.**  
   - 객체를 생성하는 코드와, 실제 실행하는 코드가 섞이지 않아 이해하기 쉬워진다.
- **테스트가 쉬워진다.**  
   - 객체를 쉽게 교체할 수 있어, 다양한 조건에서 테스트가 가능해진다.
- **유지보수가 편리해진다.**  
   - 객체 생성 방식이 변경되더라도, 실제 실행 로직에 영향을 주지 않는다.  

이를 실제 코드에서 어떻게 적용하는지 살펴본다.  

### 1. Main 분리

객체의 생성과 실행을 분리하는 가장 기본적인 방법은 **"main 함수에서 모든 객체를 미리 생성한 후, 애플리케이션에 전달하는 방식"** 이다.  

- 예제: 자동차 공장  

자동차를 운전하려면 **엔진, 바퀴, 핸들** 등 다양한 부품이 필요하다. 하지만 운전자는 자동차가 어떻게 조립되었는지 알 필요 없이, 단순히 운전만 하면 된다.  
이를 코드로 표현하면 다음과 같다.  

```python
class Engine:
    def start(self):
        print("엔진이 켜졌습니다.")

class Car:
    def __init__(self, engine):
        self.engine = engine

    def drive(self):
        self.engine.start()
        print("자동차가 출발합니다!")

# main()에서 객체를 미리 생성하고 전달
def main():
    engine = Engine()   # 엔진 생성
    car = Car(engine)   # 엔진을 자동차에 연결
    car.drive()         # 자동차 운행 시작

main()
```

- 출력 결과

```
엔진이 켜졌습니다.
자동차가 출발합니다!
```

- 정리
  - `Engine` 객체와 `Car` 객체가 분리되어 있다.  
  - `main()` 함수에서 **모든 객체를 미리 생성한 후, 필요한 곳에 전달** 한다.  
  - `Car` 클래스는 `Engine` 객체를 직접 생성하지 않고 **외부에서 주입받아 사용** 한다.  
  - 즉, **객체 생성(초기화)과 실행(사용)이 분리된 구조를 가진다.**  

### 2. Factory 패턴을 사용한 방식

때때로 객체의 생성을 `main()` 에서 직접 수행하는 것이 아니라, **전담하는 클래스를 별도로 두는 것이 유리하다.**  
이러한 방식은 **Factory 패턴** 이라고 하며, "객체를 직접 만들지 않고, **객체를 생성하는 역할을 따로 둔다**" 라는 개념이다.  

- 예제: 피자 가게  

손님이 피자를 주문할 때 직접 만드는 것이 아니라, **피자가게가 적절한 피자를 만들어 제공한다.**  
이를 코드로 표현하면 다음과 같다.  

```python
# 피자 클래스 (생성될 객체)
class Pizza:
    def __init__(self, type):
        self.type = type

    def bake(self):
        print(f"{self.type} 피자가 만들어졌습니다!")

# 피자 공장 (Factory 역할)
class PizzaFactory:
    def make_pizza(self, type):
        return Pizza(type)

# 주문 처리 시스템
class OrderProcessing:
    def __init__(self, factory):
        self.factory = factory  # 피자 공장을 받아옴

    def order_pizza(self, type):
        pizza = self.factory.make_pizza(type)  # 피자 공장을 통해 피자 생성
        pizza.bake()

# main()에서 Factory를 생성하고 주문 처리 시스템에 전달
def main():
    factory = PizzaFactory()   # 피자 공장 생성
    order_system = OrderProcessing(factory)  # 주문 시스템에 피자 공장 넘김
    order_system.order_pizza("치즈")  # 치즈 피자 주문

main()
```

- 출력 결과

```
치즈 피자가 만들어졌습니다!
```

- 정리
  - `PizzaFactory` 가 **객체(Pizza)를 생성하는 역할** 을 담당한다.  
  - `OrderProcessing` (주문 시스템)은 **피자 생성 방식에 대해 알 필요가 없다.**  
  - 새로운 피자 종류가 추가되더라도, `OrderProcessing` 코드를 수정할 필요가 없다.  
  - 즉, **객체 생성(초기화)과 실행(사용)이 완전히 분리된 구조를 가진다.**  

### 3. 의존성 주입 (Dependency Injection, DI)

의존성 주입은 객체를 직접 생성하는 것이 아니라 **외부에서 주입받아 사용하는 방식** 을 의미한다.  
이를 통해 프로그램이 더 유연해지고, 테스트가 쉬워진다.  

- 예제: 전구를 교체할 수 있는 전등  

전등을 사용할 때, **형광등** 을 끼울 수도 있고, **LED 전구** 를 끼울 수도 있다.  
전등 자체는 어떤 전구가 사용되는지 알 필요 없이, 단순히 전구를 켜는 역할만 수행하면 된다.  
이를 코드로 표현하면 다음과 같다.  

```python
# 전구 인터페이스
class LightBulb:
    def turn_on(self):
        pass

# LED 전구 구현
class LEDBulb(LightBulb):
    def turn_on(self):
        print("LED 전구가 켜졌습니다!")

# 형광등 구현
class FluorescentBulb(LightBulb):
    def turn_on(self):
        print("형광등이 켜졌습니다!")

# 전등 (LightBulb를 의존성으로 받음)
class Lamp:
    def __init__(self, bulb: LightBulb):
        self.bulb = bulb

    def switch_on(self):
        self.bulb.turn_on()

# main()에서 의존성을 주입
def main():
    bulb = LEDBulb()  # 형광등(FluorescentBulb)으로 변경 가능
    lamp = Lamp(bulb)  # 전구를 전등에 주입 (Dependency Injection)
    lamp.switch_on()

main()
```

- 출력 결과

```
LED 전구가 켜졌습니다!
```

- 정리
  - `Lamp` (전등)는 `LightBulb` (전구)가 어떤 종류인지 알 필요가 없다.  
  - `Lamp` 객체는 직접 전구를 생성하지 않고, **외부에서 주입받아 사용한다.**  
  - 형광등(FluorescentBulb)으로 변경하고 싶다면, `Lamp` 클래스를 수정할 필요 없이, 주입하는 객체만 변경하면 된다.  

### 4. 요약

| 개념             | 설명                        | 예제                        |
|----------------|---------------------------|---------------------------|
| **Main 분리**    | 객체를 `main()` 에서 미리 만들고 전달 | 자동차 공장에서 자동차를 만든 후 운전     |
| **Factory 패턴** | 객체 생성을 Factory에 위임        | 피자 가게에서 피자 생성             |
| **의존성 주입**     | 객체를 직접 만들지 않고 외부에서 주입     | 전등에 LED 전구를 끼우거나 형광등으로 변경 |

<br>

## 자바 프록시

### 1. 자바 프록시(Proxy)란?

프록시(Proxy)란 **실제 객체 대신 요청을 받아 처리하는 객체** 를 의미한다.  
이를 통해 객체의 원래 기능을 변경하지 않고 **추가적인 기능(로깅, 트랜잭션 관리 등)을 쉽게 적용할 수 있다.**  

프록시를 활용하면 다음과 같은 장점을 얻을 수 있다.  
- **객체를 감싸서 실행하기 때문에, 메서드 호출 전후에 특정 작업을 추가할 수 있다.**  
- **기존 코드(Business Logic)를 수정하지 않고도 기능을 확장할 수 있다.**  

### 2. JDK 동적 프록시(JDK Dynamic Proxy)

Java에서는 기본적으로 **JDK 동적 프록시** 를 제공하며, 이는 **인터페이스 기반으로 동작** 한다.  

- 인터페이스(Bank) 정의

```java
import java.util.Collection;

public interface Bank {
    void transferMoney();
}
```

- 구현 클래스(BankImpl) 정의

```java
public class BankImpl implements Bank {
    public void transferMoney() {
        System.out.println("계좌 이체 중...");
    }
}
```

- 프록시 핸들러(BankProxyHandler) 구현

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class BankProxyHandler implements InvocationHandler {
    private Bank bank;

    public BankProxyHandler(Bank bank) {
        this.bank = bank;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("[로그] " + method.getName() + " 실행 시작");
        Object result = method.invoke(bank, args);
        System.out.println("[로그] " + method.getName() + " 실행 종료");
        return result;
    }
}
```

- 프록시 객체 생성 및 사용

```java
import java.lang.reflect.Proxy;

public class Main {
    public static void main(String[] args) {
        Bank bank = (Bank) Proxy.newProxyInstance(
            Bank.class.getClassLoader(),
            new Class[]{Bank.class},
            new BankProxyHandler(new BankImpl())
        );

        bank.transferMoney();
    }
}
```

- 출력 결과

```
[로그] transferMoney 실행 시작
계좌 이체 중...
[로그] transferMoney 실행 종료
```

JDK 프록시의 문제점은 다음과 같다.
- 인터페이스가 없는 일반 클래스(POJO)는 JDK 프록시를 사용할 수 없다.
- 프록시 설정이 복잡하며 코드가 길어진다.
- 많은 클래스를 프록시로 감싸야 하는 경우 코드 유지보수가 어렵다.


### 3. CGLIB을 활용한 클래스 기반 프록시

JDK 프록시는 **인터페이스를 기반으로 동작** 하기 때문에,  
**인터페이스가 없는 일반 클래스(POJO)에는 적용할 수 없다.**  
이를 해결하기 위해 **CGLIB(Code Generation Library for Java) 프록시** 가 사용된다.  

- CGLIB 프록시 예제

```java
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import java.lang.reflect.Method;

public class BankCglibProxy implements MethodInterceptor {
    private Object target;

    public BankCglibProxy(Object target) {
        this.target = target;
    }

    public Object createProxy() {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(target.getClass());
        enhancer.setCallback(this);
        return enhancer.create();
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("[로그] " + method.getName() + " 실행 시작");
        Object result = proxy.invokeSuper(obj, args);
        System.out.println("[로그] " + method.getName() + " 실행 종료");
        return result;
    }
}
```

CGLIB을 활용하면 인터페이스 없이도 클래스 자체를 감싸 프록시를 적용할 수 있다.  
하지만, 프록시 설정이 여전히 복잡하며 코드가 많아진다는 단점이 있다.  

<br>

## 순수 자바 AOP 프레임워크
AOP(Aspect-Oriented Programming, 관점 지향 프로그래밍)는 공통 기능을 모듈화하여 한 곳에서 관리하는 기법이다.  
"관점(Aspect)" 이라는 단어가 사용된 이유는 소프트웨어의 핵심 로직(Core Logic)과 공통 기능(Cross-Cutting Concerns)을 서로 다른 "관점"에서 바라보도록 설계했기 때문이다.

즉, 전통적인 객체 지향 프로그래밍(OOP)이 객체 단위로 코드의 책임을 분리하는 방법이라면,
AOP는 기능(관심사, Aspect) 단위로 코드를 분리하는 방법이다.

### 1. Spring AOP의 필요성

JDK 프록시나 CGLIB을 직접 구현하면 코드가 복잡해지고 유지보수가 어려워진다.  
이를 해결하기 위해 **Spring AOP와 같은 프레임워크가 등장** 하였다.  

Spring AOP는 다음과 같은 기능을 제공한다.  
- **프록시 객체를 자동으로 생성하여 AOP 적용을 쉽게 만든다.**  
- **POJO(Plain Old Java Object)에도 AOP를 적용할 수 있다.**  

### 2. Spring AOP 적용 예제

- AOP 설정 (LoggingAspect)

```java
@Aspect
@Component
public class LoggingAspect {
    @Before("execution(* com.example.bank.Bank.transferMoney(..))")
    public void logBefore() {
        System.out.println("[로그] 계좌 이체 시작");
    }

    @After("execution(* com.example.bank.Bank.transferMoney(..))")
    public void logAfter() {
        System.out.println("[로그] 계좌 이체 종료");
    }
}
```

- 프록시 없이 순수 POJO 사용

```java
@Component
public class Bank {
    public void transferMoney() {
        System.out.println("계좌 이체 중...");
    }
}
```

- Spring Boot 실행 코드

```java
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(Main.class, args);
        Bank bank = context.getBean(Bank.class);
        bank.transferMoney();
    }
}
```

- 출력 결과

```
[로그] 계좌 이체 시작
계좌 이체 중...
[로그] 계좌 이체 종료
```

### 3. Spring AOP의 장점

- **프록시 객체를 자동으로 생성하여 개발자의 부담을 줄인다.**  
- **POJO를 유지하면서 AOP 기능을 적용할 수 있다.**  
- **Spring에서는 AOP를 활용하여 보안, 트랜잭션, 로깅 같은 공통 기능을 쉽게 관리할 수 있다.**  
