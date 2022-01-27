## 스프링 AOP 공부하기

<br>

## 스프링 AOP란?
AOP ( Aspect-oriented-programming )은 OOP를 보완하는 수단이며 대칭하는 개념이 아니다, 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법

![aop 기법 설명](../images/Screenshot%202020-08-03%20at%2023.37.05.jpg)

위 그림을 살펴보자 각 클래스에 비슷한 기능을 하는 요소들을 Aspect로 묶어서 어디에서 동작해야하는지를 표기해주는 것이다.

<br>

## AOP 용어

AOP를 공부하다보면 용어때문에 헷갈리는 경우가 많다, 모든 AOP용어를 완벽하게 이해해야 하지는 않다. 큰 흐름과 의미만 알고있으면 된다.

<br>

* Aspect : 묶인, 모여있는 모듈화 되어있는 기능이다.
* Advice: 해야할 일들, 기능이다.
* Pointcut: 어디에 적용해야 하는지의 정보, 예를들어 클래스A에 register 라는 메서드를 호출할 때 에만 특정 어드바이스를 적용하겠다. 라는 식의 정보이다.
* Target: 적용이 되는 대상(클래스A, 클래스B, 클래스C)
* Join Point: 직역하는 합류점 정도로 말할 수 있다. 우리가 가장 흔하게 실질적으로 마주하는 JoinPoint의 예시가 바로 메서드를 실행하는 시점이다. 메서드를 실행할 때 이 어드바이스를 끼워넣어라 라고하는 끼어들 수 있는 지점이 Join Point 이다, 여러가지의 다양한 JoinPoint가 있다 생성자를 호출하기 직전, 생성자를 생성했을 때, 필드에 접근하기전, 필드에서 값을 가져갔을때 등등, 끼어들 수 있는 합류지점이 Join Point 이다. 구체적으로 어디에 적용할지를 정하는 것이 Pointcut 이라면 Join Point 조금더 명세에 가까운 개념이라고 볼 수 있다.

```
예를들어 MemberService의 hello()라는 메소드 실행 전,후에 hello랑 bye를 출력하는 일을 한다고 가정

이때 MemberService 라는 Bean이 Target
"hello() 메소드 실행 전,후" 가 Point cut
"메소드 실행 전,후"라는게 Join Point
"hello랑 bye를 출력하는 일"이 Advice

조인포인트가 메타적인 정보라고 생각하시면 되고 포인트컷이 좀 더 구체적인 적용 지점이라고 이해하면 된다.
```

<br>

## AOP 적용방법

AOP는 비슷한 기능들을 묶는 것이라고 했다, 그럼 이 묶인 기능들 Aspect가 적용이되어야 하는데 적용하는 방법은 아래의 3가지 경우이다.

* 컴파일 타임 : 컴파일 할 때 AOP가 적용되어있는 바이트 코드가 생성되는 것이다. 즉 컴파일할 때 AOP 기능을 같이넣은채로 컴파일 하는 것이다.
* 로드타임 : 컴파일과는 다르게 원본대로 컴파일 하되 타겟파일을 로딩할 때, 로딩하는 시점에 바이트 코드를 변경해 적용하는 것이다. 로딩한 JVM 메모리에는 AOP가 적용된 코드가 들어가 있게 되는 것이다.
* 런타임 : spring AOP가 사용하는 방법이다, target에 해당하는 bean을 만들때 proxy bean을 만들고 프록시 빈이 실제 타겟으로 적용될 빈의 메소드를 실행하기 직전에 Advice를 끼워서 실행하는 것이다.

각각 장단점이 있는데 컴파일 타임으로 적용하려면 이미 컴파일되니까 성능상에 손실은 없겠지만 별도의 컴파일 과정을 거쳐야 한다.
로드타임에 적용하려면 클래스를 읽어들이는 시점에 약간의 부하가 생길수 있다는점과 로드타임에 자바 에이전트를(위버) 를 설정해주어야 한다는 점이 있고
런타임에 적용하는 방법은 초기 bean을 만드는 상태에서의 약간의 부하가 발생할 수 있다는 점과(어플리케이션에서 요청이 들어올 때 마다 성능을 잡아먹는건 아님)
아무설정이 없어도 된다라는 장점도 있고 문법이 쉽다는 점도 있고 별도의 학습 비용이 적다는 장점도 있다.

<br>

## 스프링 AOP (프록시 기반 AOP)

* 프록시 기반의 AOP 구현체
* 스프링 빈에만 AOP를 적용할 수 있다.
* 모든 AOP 기능을 제공하는 것이 목적이 아니라, 스프링 IOC와 연동하는 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책 제공이 목적

![프록시 객체 설명](../images/Screenshot%202020-08-04%20at%2000.33.11%20%5B2%5D.jpg)

위 그림이 프록시패턴을 설명하는 이미지 이다. 원래 프록시 패턴은 인터페이스가 있고 클라이언트는 인터페이스의 타입으로 프록시 객체를 사용하게 된다.
그리고 프록시 객체는 원래 객체인 Real Subject를 가지고 있는다. 이렇게 사용하는 이유는 전급제어 와 부가기능 추가를 이유로 한다.

<br>

#### EventService.java - interface (Subject)
```
public interface EventService {
    void createEvent();
    void publishEvent();
}
```

#### SimpleEventService.java - class (Real Subject)
```
@Service
public class SimpleEventService implements EventService {
    @Override
    public void createEvent(){
        Thread.sleep(1000);
    System.out.println("Created an event");        
    }

  @Override
  public void createEvent(){
    Thread.sleep(2000);
    System.out.println("Published an event");
  }
}
```

#### AppRunner.java  (Client)
```
@Component
public class AppRunner implements ApplicationRunner {
    @Autowried
    EventService eventService; // 구현체를 받는게아닌, 인터페이스가 있는경우 인터페이스 타입으로 주입을 받는게 좋다고 한다.

    @Override
    public void run(ApplicationArguments args) throws Exception{
        eventService.createEvent();
    eventService.publishEvent();
    }
}
```
위 코드처럼 클라이언트, 서브젝트, 리얼 서브젝트가 준비되어 있는 상황에서 AOP를 이용해서 코드를 건드리지 않고 추가하는것이 가능하다.
코드를 실행하는 시간을 측정하고 싶다면 프록시패턴을 이용해 시간을 측정하는 기능을 추가할 수 있다.

#### ProxySimpleEventService - AOP 적용하기
```
// 같은 interface를 구현해야 한다.
@Primary
@Service
public class ProxySimpleEventService implements EventService {
    @Autowired
    SimpleEventService simpleEventService;

    @Override
    public void createEvent(){
        long begin = System.currentTimeMillies();
        simpleEventService.createEvent(); // 델리게이션, 위임함
        System.out.println(System.currentTimeMillies() - begin); // 시간을 측정하는 로직 추가
    }

    @Override
    public void publishEvent(){
    long begin = System.currentTimeMillies();
        simpleEventService.publishEvent() // 델리게이션, 위임함
        System.out.println(System.currentTimeMillies() - begin); // 시간을 측정하는 로직 추가
    }
}
```

완벽한 프록시 패턴이 되었다, ProxySimpleEventService 라는 프록시는 SimpleEventService 라는 Real Subject를 주입받았고 메서드를 실행할 때 리얼 서브젝트에게
실행해야할 일을 위임하였고 부가적인 기능을 구현하였다, 클라이언트는 주입받을 때 EventService를 주입 받지만 @Primary가 마킹되어있는 SimpleEventService를
주입받아서 사용하게 될것이다.

그런데 이런 방식 역시 프록시 객체를 만들어야 하고 모든 메서드를 델리게이션 해주어야하고 시간을 측정하기 위한 중복코드가 발생하고 이런 여러 문제가 생길 것이다. 

<br>

## 스프링 AOP 사용하기

위에서 이야기한 방법은 여러 단점이 있으므로 스프링에서 제공하는 AOP를 적용해 코드를 개선하자
우선 spring-boot-starter-aop 라고하는 스프링 AOP 의존성 모듈을 받아주어야 한다.


#### PerfAspect.java
```
@Component // 빈으로 등록
@Aspect // Aspect class 라는것을 어노테이션으로 알려준다. (해야할일을 모아놓은 클래스)
Public class PerfAspect {
    // Around 어노테이션이 포인트컷을 지정하는 행위라고 볼 수 있다.
    // me.whiteship 패캐지 하위에 있는 EventService안에 들어있는 모든 메서드에 하위동작(logPerf)를 사용하게 되는 것이다.
    @Around(“execution(* me.whiteship..*.EventService.*(..)”) 
    Public Object logPerf(ProceedingJoinPoint pjp) throws Throwable{ // pjp는 어드바이스가 적용되는 대상이라고 볼 수 있다. (createEvent, publishEvent)
        Long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed(); // 메서드를 실행하는것이 proceed 이다.
        System.out.println(System.currentTimeMillis() - begin);
        Return retVal;
    }
}
```
위에 코드처럼 우리가 만든 PerfAspect 라고하는 Aspect 클래스가 여러곳에 적용되어야 한다면 @Around에 execution을 사용해 적용하면 된다.
그런데 특정 메서드에서만 선별적으로 적용되길 원한다면 custom annotation을 만들어서 PerfAspect가 적용되길 원하는 곳에 우리가 만든 커스텀 어노테이션을 붙여주어도 된다.

<br>

추가로 커스텀 어노테이션 만들고 어노테이션으로 Around를 적용하자

#### PerLogging.java - 커스텀 어노테이션

```
// RetentionPolicy란 이 어노테이션 정보를 얼마나 유지할 것인가 이다, RetentionPolicy.CLASS로 주었다는 뜻은 컴파일하고난 .class 파일에도 이 어노테이션이 남아있게 된다는 뜻이다. 기본이 CLASS이다.
@Retention(RetentionPolicy.CLASS)
@Target(ElementType.METHOD) // 자바 어노테이션을 커스텀으로 만들때 적용대상이 로컬변수인지 메서드인지 필드인지 명시적으로 알려주는것이다.
@Documented // Javadoc이라고 코드를 문서화 하는 기능이 있다, @Documented를 가진 커스텀 어노테이션을 사용하면 커스텀한 어노테이션(여기선 PerLogging)의 정보를 보여준다.
Public @interface PerLogging {
}
```
위 코드를 보자 어노테이션을 만들었다, excution 으로 @Around를 적용하는것 대신 어노테이션으로 구분해주기 위해 커스텀 어노테이션을 만든것이다.

<br>

#### PerfAspect.java
```
@Component
@Aspect
Public class PerfAspect {
    @Around(“@annotation(PerLogging)") // 어노테이션으로 적용되게 바꾸었다.
    Public Object logPerf(ProceedingJoinPoint pjp) throws Throwable{
        Long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        Return retVal;
    }
}
```

@Around의 적용 범위를 PerLogging라는 어노테이션을 가진 대상에 어드바이스를 적용하게 변경하였다.


<br>

```
@Primary
@Service
public class ProxySimpleEventService implements EventService {
    @Autowired
    SimpleEventService simpleEventService;


    @PerLogging // 어노테이션으로 AOP 적용
    @Override
    public void createEvent(){
        long begin = System.currentTimeMillies();
        simpleEventService.createEvent(); // 델리게이션, 위임함
        System.out.println(System.currentTimeMillies() - begin);
    }


    @PerLogging // 어노테이션으로 AOP 적용
    @Override
    public void publishEvent(){
    long begin = System.currentTimeMillies();
        simpleEventService.publishEvent() // 델리게이션, 위임함
        System.out.println(System.currentTimeMillies() - begin);
    }
}
```

각각의 메서드에 AOP를 적용해야 한다면 어노테이션을 붙여서 사용하는것이 더욱 편리할 것이다.

<br>

```
@Around(“bean(simpleEventService)")
```

이렇게 어라운드에 bean을 이용해서 적용해 줄수도 있다.

<br>

## 참고자료

[스프링 프레임워크 핵심 기술 - 백기선](https://www.inflearn.com/course/spring-framework_core/dashboard)
