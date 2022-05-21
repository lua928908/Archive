# 입사후 자바 공부하기

<br>

* 이직 후 스프링부트, JPA를 주 스택으로 사용하게 되었다
* 공부한 내용을 기록하자
* 혼자 복습을 위한 아카이브 용도가 주 목적이기에 모호한 설명이나 용어 사용이 있다
* 갓영한님의 강의는 늘 [구매](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)하자

<br>


기억하기

1. 실무에서는 무조건 단순하게 명확하게 개발해야 한다 헷갈리게 만들면 안된다. (개인적으로 일관되게 만드는것도 중요 하게 생각 함)
2. 정적인 의존관계는 변경하지 않은채로 동적인 의존관계 및 주입을 변경할 수 있어야 한다. (이게 가능해야 SOLID 원칙을 잘 지킨 것 이라고 볼 수 있다.)
3. 가장 좋은 에러는 컴파일 시점에서 발생하는 에러다 런타임 시점에서 실행해 봤을 때 발생하는 에러는 큰 비용으로 이어질 수 있다.


용어 정리
- TDD -> 테스트 주도 개발 => 테스트 코드를 먼저 짠다(검증 부터 고민한다) 이 후 실제 개발을 만드는 식에 개발 방법
- 서비스 -> 비즈니스 로직이 포함되는 부분
- 레포지터리 -> 개발스러운, 조금 더 부품 적인 영역
- 도메인 -> 해당 도메인의 스펙 명세서 같은 느낌
- DI = 디펜던시 인젝션, 의존관계를 외부로 부터 주입받는 행위, 즉 생성자를 내가 직접 만들어서 쓰는게 아니라 외부에서 생성자를 넣어줘서 받아 쓴다 (실행 될 때 마다 똑같은 인스턴스를 사용하기 위한 방법으로 쓰는 경우 있음 -> 테스트 할 때 같은 인스턴스로 테스트 하기 위해)


명령어
```
./gradlew build 
java -jar <빌드된 jar파일 이름>
```




@Controller 라는 어노테이션이 있으면 스프링 컨테이너가 해당 Bean을 만들어서 관리한다 즉 @Controller라는 어노테이션이 붙은 부분에 맞는 객체를 만들어 스프링이 관리하게 되는것이다
bean으로 등록해서 써야하는 이유는 스프링 컨테이너를 통해 관리되어야 하기 때문이다 그냥 new 생성자로 만들어서 써버리면 동시성 문제나 다른 스레드에서 사용할 때 문제가 될 수 있다 근데 bean으로 등록해서 스프링 컨테이너에서
받아 사용한다면 값이 변경되거나 기타 참조를 하더라도 문제가 되지 않는다 같은 객체를 참조하는 것이니까, 리덕스에서 state를 변경할 때 리덕스 스토어를 통해 값을 변경해야지 그냥 어떠한 컴포넌트에 값에 직접적으로 변경하면
안되는 것과 비슷한 개념이 아닐까 싶다



디펜던시 인젝션의 3가지 종류
디펜던시 인젝션을 받을때 3가지 방법이 있다
필드주입 = class field에 @AutoWired를 써서 필드에 바로 받는 방법인데 한번 빈이 결정되면 변경이 안된다는 단점이 있다
setter주입 = setter를 만들고 @AutoWired를 붙여서 세터로 주입받는데 public으로 열려 있어야 해서 만약 병신짓을 하면 주입받는 빈이 변경될 여지가 있다
생성자 주입 = 생성자 함수를 만들고 @AutoWired를 붙이면 생성할 때 주입 받는다, 추천하는 방법, 애플리케이션이 조립될때 스프링컨테이너가 만들어 질 때 주입받는 방식이다 (실행중 의존관계가 동적으로 바뀌는 경우는 실무에서 한번도 없었다고 함)



직접 빈으로 등록해야 하는 경우는 언제인가?

시나리오
```
1. 아직 어떤 DB를 사용할 지 결정이 안되었다
2. 회원가입을 하는 부분인 Member 관련 기능을 개발할 때 MemberRepository 부분을 인터페이스로 만들고 구현체인 MemoryMemberRepository를 만들었다.
3. 나중에 DB가 결정되면 임시로 만든 MemberRepository 대신 다른 구현체로 갈아끼워야 하는 경우가 있을 것이다
4. 이 때 기존 코드(Controller or Service) 아무것도 안건드리고 bean만 다른애로(다른 구현체로) 주입받기 위해 직접 빈으로 등록하는 경우가 발생할 수 있다
```
일반적으로는 컴포넌트 스캔으로 빈을 등록해서 쓰는데 상황에 따라 구현클래스를 바꿔야 하는 경우 빈으로 등록해 사용한다

예제 코드

```
@Autowired 
public MemberService(MemberRepository memberRepository) { 
    this.memberRepository = memberRepository; 
}
```
원래 MemberService에서 생성자에 @Autowired를 통해 MemoryMemberRepository 라는 구현체를 빈을 주입받았음


```
package hello.hellospring; 
import hello.hellospring.repository.MemberRepository; 
import hello.hellospring.repository.MemoryMemberRepository; 
import hello.hellospring.service.MemberService; 
import org.springframework.context.annotation.Bean; 
import org.springframework.context.annotation.Configuration; 
// 직접 bean 등록하기 (@Component 를 통한 컴포넌트 스캔을 사용하지 않는 경우) 
@Configuration 
public class SpringConfig { 
    @Bean 
    public MemberService memberService(){ 
        return new MemberService(memberRepository()); 
    } 
    @Bean 
    public MemberRepository memberRepository(){ 
        // return new MemoryMemberRepository(); 
        return new DbMemberRepository();
    } 
}
```
근데 db가 결정되고 나서 bean으로 주입받는 구현체만 바꾸기 위해 DbMemberRepository를 반환하게 설정하면 기존 코드 변경없이 bean을 다른것으로
바꿀 수 있다.




중국,한국을 제외하면 글로벌에서 myBatis 보다 JPA를 훨씬 많이 쓰는데 국내에서만 유독 안쓰더라, 이유를 생각해보니 책이 없어서 기술 보급이 어려울 것 같았고 토비의 스프링에 저자 이일민이 추천을 해줘서
2년 동안 저녁시간마다 책을 집필해서 2015년에 JPA책을 냈다고 함 애정이 존나 지리는 듯

JPA를 사용할 때에는 항상 @Transaction을 사용해줘야 한다
jpa는 모두 EntityManager 를 통해 관리 된다.

```
@Transactional 
public class JpaMemberRepository implements MemberRepository{

    private final EntityManager em;

    @Autowired 
    public JpaMemberRepository(EntityManager em) { 
        this.em = em; 
    }

    @Override 
    public Member save(Member member) { 
        em.persist(member); 
        return member; 
    }


    ... 이하 생략 
}
```



JPA를 먼저 학습한 다음 spring-data-jpa를 학습해야 한다
spring-data-jpa는 JPA를 편하게 사용할 수 있도록 도와주는 도구일 뿐이다


---

AOP  이해하기

가정을 해보자 만약 상사가 우리 회사 시스템에 문제가 있는 것 같다고 모든 메서드에 실행시간을 측정하는 코드를 넣으라고 요구 한다.
그래서 파일을 다 열어서 메서드 시작/끝에 초 단위로 측정 되도록 코드를 넣었다 그런데 나중에 밀리세컨트로 변경 해달라고 한다면
다시 메서드를 하나씩 다 열어서 수정해야 할 것 이다.



문제
회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
시간을 측정하는 로직은 공통 관심 사항이다.
시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.



위 상황을 AOP로 해결하면 편리하다


```
package hello.hellospring.aop; 
import org.aspectj.lang.ProceedingJoinPoint; 
import org.aspectj.lang.annotation.Around; 
import org.aspectj.lang.annotation.Aspect; 
// Aspect 어느테이션을 줘야 AOP로 쓸 수 있음 
@Aspect 
public class TimeTraceAop { 
    @Around("execution(* hello.hellospring..*(..))") 
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable { 
        long start = System.currentTimeMillis(); 
        System.out.println("START: " + joinPoint.toString()); 
        try{ 
            Object result = joinPoint.proceed(); 
            return result; 
        }finally { 
            long finish = System.currentTimeMillis(); 
            long timeMs = finish - start; 
            System.out.println("END: " + joinPoint.toString() + "" + timeMs + "ms" ); 
        } 
    } 
}
```

위 코드처럼 AOP 패키지를 따로 만들어서 지정하면 모든 메서드에 시간을 조회하는 코드를 삽입할 수 있다.






---



테스트 코드 예제

```
package hello.core.discount; 
import hello.core.member.Grade; 
import hello.core.member.Member; 
import org.assertj.core.api.Assertions; 
import org.junit.jupiter.api.DisplayName; 
import org.junit.jupiter.api.Test; 
import static org.assertj.core.api.Assertions.*; 
import static org.junit.jupiter.api.Assertions.*; 
class RateDiscountPolicyTest { 
    RateDiscountPolicy discountPolicy = new RateDiscountPolicy(); 
    @Test 
    @DisplayName("VIP는 10% 할인이 적용되어야 한다.") 
    void vip_o() { 
        //given 
        Member member = new Member(1L, "memberVIP", Grade.VIP); 
        //when 
        int discount = discountPolicy.discount(member, 10000); 
        //then 
        assertThat(discount).isEqualTo(1000); 
    } 
    @Test 
    @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.") 
    void vip_x(){ 
        //given 
        Member member = new Member(2L, "memberBASIC", Grade.BASIC); 
        //when 
        int discount = discountPolicy.discount(member, 10000); 
        //then 
        assertThat(discount).isEqualTo(0); 
    } 
}
```


---


1. 웹사이트에서 상품을 주문함
2. 주문 컨트롤러가 서비스 호출
3. 주문서비스가 로직을 수행
4. 이때 로직중 할인정책에 대한 로직은 중요하고 바뀔 여지가 있기 때문에 별도로 분리해서 개발한다면 orderService는 discountPolicy를 주입받아 사용해 한다. 즉 의존관계를 맺게 된다.
5. discountPolicy의 구현체가 바뀌면 코드상에서도 주입받는 부분 discountPolicy = new FixedDiscountPolicy() 부분을 수정해야 햔다.
6. 이 행위 자체가 DIP 위반이고 객체지향적인 개발이 아닌 것이다 생각해보면 요구사항이 바뀌면 당연히 어느부분 에서는 코드가 변해야 하지 않는가?
7. 이런 여러가지 문제를 해결하는 방법이 스프링에서는 의존성을 주입받는 것이다 즉, IOC 컨테이너나 bean 같은 것들 말이다.



---


싱글톤이란

스프링은 태생적으로 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다. 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.
예를들어 배달의민족 서비스를 이용하는 사람이 많아지면 동시에 주문하는 사람이 10명이 될수도 있고 100명이 될수도 있는데 요청이 들어올때 마다
orderService 를 만들어서 DI 컨테이너가 주입해준다고 생각해보자,  클라이언트가 서버로 요청을 할 때 마다 새로운 객체를 생성해서 주입해주고
JVM 메모리에 계속 새로운 객체가 생성되서 올라가는 것이다.

스프링없는 순수 DI 컨테이너는 AppConfig는 요청할 때 마다 객체를 생성하고 소멸하게 된다. 해결방안이 객체를 딱 1개만 생성하고 공유하도록 설계하는 것이다.


```
static 키워드를 붙이는 변수는 메모리에 올라가서 공유된다, new로 인스턴스를 생성 하더라도 공유 된다.
static 키워드를 붙이는 메서드는 new로 인스턴스를 생성하지 않아도 메서드에 접근해 사용할 수 있다. 그래서 주로 유틸성 메서드에 많이 사용된다.


class Util { 
    public static String getCurrentDate(String fmt) { 
        SimpleDateFormat sdf = new SimpleDateFormat(fmt); 
        return sdf.format(new Date()); 
    } 
} 
public class Sample { 
    public static void main(String[] args) { 
        System.out.println(Util.getCurrentDate("yyyyMMdd"));  // 오늘 날짜 출력 (new로 생성하지 않았음에도 바로 메서드에 접근 가능)
    } 
}
```


싱글톤 패턴 문제점
- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
- 의존관계상 클라이언트가 구체 클래스에 의존한다. DIP를 위반한다.
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 테스트하기 어렵다.
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 자식 클래스를 만들기 어렵다.
- 결론적으로 유연성이 떨어진다.
- 안티패턴으로 불리기도 한다.

이러한 문제를 스프링에서는 싱글톤 컨테이너를 만드는 것으로 해결했다, 싱글톤 객체를 관리해주는 별도의 컨테이너를 만드는 것이다.
스프링 에서는 싱글톤 패턴으로 만들기위해 DIP, OCP를 위반하지 않으면서도 구현하는 코드를 만들지 않기 위해 CGLIB 같은 라이브러리를 사용하는 것.
@Configuration 어노테이션이 붙어야 new AnnotationConfigApplicationContext() 를 할 때 CGLIB 라이브러리를 사용해 싱글톤 패턴을 보장하는 것이다.



싱글톤 방식의 주의점


- 싱글톤에는 중요한 주의점이 있는데 무상태(stateless)로 설계해야 한다. 싱글톤 패턴이든 스프링 같은 싱글톤 컨테이너를 사용하든 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 객체 인스턴스를 공유하므로 객체 상태를 유지하게 설계하면 안된다.
    - 특정 클라이언트에 의존적인 필드가 있으면 안된다.
    - 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
    - 필드 대신에 자바에서 공유되지 않는, 지역변수 파라미터, ThereadLocal 등을 사용해야 한다.
- 스프링 빈의 필드에 공유 값을 설정하면 매우 심각한 장애가 발생할 수 있다.


---


주입을 생성자로 받아야 하는 이유
1. 수정자(setXXX) 메서드를 사용하면 다른 곳에서 개발자가 수정할 가능성이 있다, 의도와 다르게 사용할 여지 자체를 막아야 좋은 코드
2. 필드주입으로 바로 받으면 편해보이지만 안티패턴이다, 스프링부트테스트가 아니라 순수 테스트코드를 작성할 때 주입받을 방법이 없어진다 DI컨테이너 안에서 테스트를 해야되는 문제가 생긴다
3. 테스트코드 작성시 의존관계가 명확하게 보이지 않는다, 주입받아야 할 값이 없거나 nullPointExceltion이 발생할 여지가 있을 때 생성자로 주입을 받으면 바로 에러임이 보이지만 수정자주입으로 만들면 이런부분이 코드를 까봐야 알 수 있게 된다.
4. final 키워드를 붙일 수 있다, 상수라는 것을 명시할 수 있고 필수값이라는 것을 컴파일 할 때 부터 오류 여부를 알 수 있다.


---

Lombok 사용하기

롬복 사용을 위한 build.gradle 예제
```
plugins { 
	id 'org.springframework.boot' version '2.6.6' 
	id 'io.spring.dependency-management' version '1.0.11.RELEASE' 
	id 'java' 
} 
group = 'hello' 
version = '0.0.1-SNAPSHOT' 
sourceCompatibility = '11' 
// lombok 설정 
configurations { 
	compileOnly { 
		extendsFrom annotationProcessor 
	} 
} 
repositories { 
	mavenCentral() 
} 
dependencies { 
	implementation 'org.springframework.boot:spring-boot-starter' 
	// lombok 라이브러리 추가 
	compileOnly 'org.projectlombok:lombok' 
	annotationProcessor 'org.projectlombok:lombok' 
	testCompileOnly 'org.projectlombok:lombok' 
	testAnnotationProcessor 'org.projectlombok:lombok' 
	testImplementation 'org.springframework.boot:spring-boot-starter-test' 
} 
tasks.named('test') { 
	useJUnitPlatform() 
}
```

- Intelli J - plugins  메뉴에서 lombok을 설치하고 활성화 해준다.
- Shift 2번 눌러서 글로벌 서치창에서 annotation processor 옵션을 검색 후 활성화 해준다.

1. final키워드를 붙이면 필수이 된다 (상수라는 의미기 때문에 초기값이 있어야 함)
2. @RequiredArgsConstructor 라는 어노테이션을 붙이면 final 키워드가 붙은 필드를 파라미터로 받는 생성자를 생성해 준다.
3. bean 이면서 생성자 함수가 1개인(오버로드 되지 않은) 메서드는 @Autowired를 붙이지 않아도 된다.
4. 결과적으로 @RequiredArgsConstructor 어노테이션을 붙이면 final 키워드가 붙은 필드를 생성해주는 코드가 자동으로만들어 지게 된다.



---


조회된 빈이 2개 이상인 경우 해결법 (@Autowired,  @Qualifier,  @Primary 사용)

```
package hello.core.order; 
import hello.core.discount.DiscountPolicy; 
import hello.core.member.Member; 
import hello.core.member.MemberRepository; 
import hello.core.member.MemoryMemberRepository; 
import org.springframework.beans.factory.annotation.Autowired; 
import org.springframework.stereotype.Component; 

@Component 
public class OrderServiceImpl implements OrderService{ 
    private final MemberRepository memberRepository; 
    private final DiscountPolicy discountPolicy; 
    @Autowired 
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) { 
        this.memberRepository = memberRepository; 
        this.discountPolicy = discountPolicy; 
    } 
    @Override 
    public Order createOrder(Long memberId, String itemName, int itemPrice) { 
        Member member = memberRepository.findById(memberId); 
        int disCountPrice = discountPolicy.discount(member, itemPrice); 
        return new Order(memberId, itemName, itemPrice, disCountPrice); 
    } 
    // 테스트 용도 
    public MemberRepository getMemberRepository(){ 
        return memberRepository; 
    } 
}
```

위 와 같은 코드가 있다. OrderServiceImpl를 생성자로 주입받게 되는데 이 때 DiscountPolicy discountPolicy 부분이 문제가 된다.
DiscountPolicy  타입으로 discountPolicy  라는 파라미터를 주입받아 생성자를 만들게 되는데 DiscountPolicy 타입에는 자식타입인
FixedDiscountPolicy와 RateDiscountPolicy 2가지 자식타입이 존재하기 때문이다. @Autowired로 자동주입을 받을 때 타입으로 조회를 해서 넣어준다.
부모타입으로 조회하면 자식타입까지 모두 조회하기 때문에 문제가 된다.
즉, FixedDiscountPolicy와 RateDiscountPolicy가 모두 bean으로 등록된 상태에서 부모타입으로 자동주입을 해놓으면 여러개의 빈이 조회되는 문제가 생긴다.

이 문제를 해결하기 위해 자식타입을 지정해버리면  DIP를 위배하고 유연성이 떨어진다. 역할에 의존해야지 구체적인 것에 의존하면 안된다.
이런 문제를 해결하기 위한 방법이 @Quilifier @Primary 라는 어노테션이다.

해결방법
- @Autowired 필드 명 매칭
- @Qualifier  ->  @Qualifier끼리 매칭  ->   빈 이름 매칭
- @Primary 사용

@Autowired 를 사용
```
@Autowired는 타입으로 조회하지만 조회 결과가 여러개면 필드이름이 매칭되는 것을 사용한다.

// 변경 전
@Autowired 
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) { 
    this.memberRepository = memberRepository; 
    this.discountPolicy = discountPolicy; 
}

// 변경 후
// DiscountPolicy 타입이 여러개지만 필드명이 일치하는 FixedDiscountPolicy 사용
@Autowired 
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy FixedDiscountPolicy) {
    this.memberRepository = memberRepository; 
    this.discountPolicy = discountPolicy; 
}
```


@Qualifier  ->  @Qualifier끼리 매칭  ->   빈 이름 매칭
```
추가 구분자를 붙여주는 밥법이다. 주입시 추가적인 방법을 제공하는 것이지 빈 이름이 변경되는 것은 아니다

// RateDiscountPolicy 에다가 @Qualifier를 이용해 별도의 이름(구분자)를 지정해 준다.
@Component 
@Qualifier("rateDiscountPolicy") 
public class RateDiscountPolicy implements DiscountPolicy{ 
    private int discountPercent = 10; 
    @Override 
    public int discount(Member member, int price) { 
        if(member.getGrade() == Grade.VIP){ 
            return price * discountPercent / 100; 
        }else{ 
            return 0; 
        } 
    } 
}

// 생성자에서 주입받을 때(빈이 여러개가 조회되는 부분에서) @Qualifier로 가져올 빈을 지정한다.
@Autowired 
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("rateDiscountPolicy") DiscountPolicy discountPolicy) {// 빈 이름을 지정한다.
    this.memberRepository = memberRepository; 
    this.discountPolicy = discountPolicy; 
}
```
만약 @Qualifier("rateDiscountPolicy")를 찾지 못한다면 rateDiscountPolicy라는 이름의 스프링 bean이 있는지 찾는다.  빈중에도 없으면 exception 오류 발생


@Primary
```
자동주입을 할 때 동일한 이름의 빈이 있다면 @Primary가 지정된 빈을 사용하게 한다, 우선순위를 지정하는 것이다.

@Component  
@Primary
public class RateDiscountPolicy implements DiscountPolicy{ 
    private int discountPercent = 10; 
    @Override 
    public int discount(Member member, int price) { 
        if(member.getGrade() == Grade.VIP){ 
            return price * discountPercent / 100; 
        }else{ 
            return 0; 
        } 
    } 
}


// DiscountPolicy 타입의 bean이 2개지만 @Primary로 지정된 RateDiscountPolicy를 사용 함
@Autowired 
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {  
    this.memberRepository = memberRepository; 
    this.discountPolicy = discountPolicy; 
}
```



---

Bean의 라이프사이클 - 초기화, 소멸

빈이 생성될때와 초기화(값을 초기화) 하는 동작을 하는 것을 서로 나누는게 좋다
생성은 메모리에 할당해서 객체를 생성하기만 하고 초기화는 생성된 값을 사용해 외부 커넥션을 맺는 등
무거운 동작을 하는 경우라면 유지보수 측면에서도 특히나 구분짓는게 좋다. 빈이 생성되었음을 알려주는 방법이
InitializingBean = InitializingBean을 implements (생성 시점)
disposableBean = DisposableBean을 implements (종료하는 시점)

```
package hello.lifecycle; 
import org.springframework.beans.factory.DisposableBean; 
import org.springframework.beans.factory.InitializingBean; 
public class NetworkClient implements InitializingBean, DisposableBean { 
    private String url; 
    public NetworkClient(){ 
        System.out.println("생성자 호출, URL = " + url); 
    } 
    public void setUrl(String url){ 
        this.url = url; 
    } 
    // 서비스 시작시 호출 
    public void connect(){ 
        System.out.println("connect: " + url); 
    } 
    public void call(String message){ 
        System.out.println("call: " + url + " message = " + message); 
    } 
    // 서비스 종료시 호출 
    public void disconnect(){ 
        System.out.println("close " + url); 
    } 
    // 
    @Override 
    public void afterPropertiesSet() throws Exception { 
        connect(); 
        call("초기화 연결 메세지"); 
    } 
    @Override 
    public void destroy() throws Exception { 
        disconnect(); 
    } 
}
```


단점
- 이 인터페이스는 스프링 전용 인터페이스다, 해당 코드가 스프링 전용 터페이스에 의존적이게 된다.
- 초기화 소멸 메서드의 이름을 변경할 수 없다
- 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다
- 스프링 초기에 나온 방법이므로 현재는 거의 사용하지 않는 방법이다.



위 방법은 현재는 거의 사용하지 않는 레거시 해결책이다
더 나은 방법으로 bean을 등록할 때 initMethod 와 destroyMethod를 지정해줄 수 있다.
```
@Bean(initMethod = "init", destroyMethod = "close") 
public NetworkClient networkClient(){ 
    NetworkClient networkClient = new NetworkClient(); 
    networkClient.setUrl("http://exaple.dev"); 
    return networkClient; 
}
```



결론: 이 방법을 쓰면 된다 - @PostConstruct, @PreDestroy

```
@Bean 
public NetworkClient networkClient(){ 
    NetworkClient networkClient = new NetworkClient(); 
    networkClient.setUrl("http://exaple.dev"); 
    return networkClient; 
}
```


```
package hello.lifecycle; 
import javax.annotation.PostConstruct; 
import javax.annotation.PreDestroy; 
public class NetworkClient{ 
    private String url; 
    public NetworkClient(){ 
        System.out.println("생성자 호출, URL = " + url); 
    } 
    public void setUrl(String url){ 
        this.url = url; 
    } 
    // 서비스 시작시 호출 
    public void connect(){ 
        System.out.println("connect: " + url); 
    } 
    public void call(String message){ 
        System.out.println("call: " + url + " message = " + message); 
    } 
    // 서비스 종료시 호출 
    public void disconnect(){ 
        System.out.println("close " + url); 
    } 
    @PostConstruct 
    public void init(){ 
        System.out.println("NetworkClient.init"); 
        connect(); 
        call("초기화 연결 메세지"); 
    } 
    @PreDestroy 
    public void close(){ 
        System.out.println("NetworkClient.close"); 
        disconnect(); 
    } 
}
```

위 코드처럼 @PostConstruct 어노테이션을 붙이면 빈의 라이프사이클 중 생성이후에 호출하고
@PreDestroy 어노테이션을 붙이면 빈의 라이프사이클 중 종료직전에 실행시킨다.

- @PostConstruct , @PreDestroy를 쓰면 된다.
- 수정할 수 없는 외부라이브러리인 경우 @Bean(initMethod = "", destroyMethod = "")를 쓰자



---


스프링 스코프

스프링 빈 스코프는 2가지가 존재한다, 싱글톤과 프로토타입이다.
싱글톤은 위에서 알아본 것 처럼 1개의 인스턴스를 만들고 동일한 인스턴스가 제공되는 것이 보장된다. 프로토타입은 실행할 때 마다 인스턴스를 생성한다, 대신 생성해서 제공하고 나면 DI컨테이너가 관리하지 않으므로 클라이언트가 @PreDestroy를 해야한다.

문제는 싱글톤 클래스 안에서 프로토타입인 bean이 존재하는 경우 예상과 다르게 동작한다, 싱글톤 빈 안에서 프로토타입 빈을 사용하면 생성시점에서 만든 프로토타입을 소멸하지 않고 계속 가져다 쓰기 때문에 생각한대로 동작하지 않는다.
이 문제를 해결하기 위해서는 ObjectProvider로 감싸서 주입받고 사용시 prototypeBeanProvider.getObject(); 이렇게 사용하면 된다.

```
@Scope("singleton")
static class ClientBean{
    // 그냥 주입받으면 호출할때 마다 새로생성하지 않기 때문에 ObjectProvider로 감싸서 주입받는다.
    @Autowired
    private ObjectProvider<PrototypeBean> prototypeBeanProvider;

    public int logic(){
        PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
}
```



웹에서 사용되는 웹스코프도 있다.

- request: HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성 된다.
- session: HTTP Session과 동일한 생명주기를 가진다.
- application: 서블릿 컨텍스트( ServletContext )와 동일한 생명주기를 가지는 스코프
- websocket: 웹 소켓과 동일한 생명주기를 가지는 스코프

컨트롤러나 서비스에서 @scope(value="request") 이런 형태의 리퀘스트를 주입받아 사용하는 경우 스프링 어플리케이션이 구동될 때 문제가 생긴다.
request는 http요청이 생겨야 만들어 지는데 어플리케이션이 실행되는 시점에서 빈으로 주입하려고 하니 발생하는 오류다. 이 때 해결 방법으로 ObjectProvider로 감싸서 사용해도 되지만
더 간략한 방법이 @scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
이렇게 가짜 프록시 클래스를 만들어 사용하는 것이다.

```
package hello.core.common;

import org.springframework.context.annotation.Scope;
import org.springframework.context.annotation.ScopedProxyMode;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import java.util.UUID;


@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS) // HTTP요청 당 하나씩 생성되고 response 되면 소멸 된다.
public class MyLogger {

    private String uuid;
    private String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

    public void log(String message){
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }

    @PostConstruct
    public void init(){
         uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create: " + this);
    }

    @PreDestroy
    public void close(){
        System.out.println("[" + uuid + "] request scope bean close: " + this);
    }
}
```


---


동시요청 - 멀티 쓰레드

Thread에 대한 이해는 백엔드 개발자에게 매우 중요하다.

장점
- 동시 요청을 처리할 수 있다.
- 리소스(CPU, 메모리)가 허용할 때 까지 처리 가능
- 하나의 쓰레드가 지연 되어도 나머지 쓰레드는 정상 동작
  단점
- 쓰레드는 생성 비용이 매우 비싸다 (고객이 요청할 때 마다 쓰레드를 생성하면 응답 속도가 늦어짐)
- 쓰레드는 컨텍스트 스위칭 비용이 발생한다
    -  1코어에 2쓰레드 라면, 코어는 쓰레드를 전환하며 작업을 수행한다
    - 이런 작업 전환 비용을 컨텍스트 스위칭 비용 이라고 한다.
    - 쓰레드가 적으면 상관 없는데, 수가 많아지면 비용이 점점 커진다
- 쓰레드 생성에 제한이 없다 (고객 요청이 많아지면 CPU, 메모리 임계점이 넘어서 서버가 죽을 수 있다.)

더 나은 방법으로 쓰레드 풀을 사용한다.
미리 쓰레드풀에 쓰레드를 만들어 놓고 사용된 쓰레드는 반납한다.  DB의 커넥션 처럼


Thread Pool
- was의 주요 튜닝 포인트는 max thread 수 이다.
- 이 값을 너무 낮게 잡으면 동시 요청이 많을 때 서버 리소스는 여유롭지만 클라이언트는 응닫 지연
- 이 값을 너무 높게 잡으면 CPU, 메모리 초과로 서버 오류
- 장애 발생시
    - 클라우드면 서버증설 후 튜닝
    - 클라우드가 아니면 튜닝
- 쓰레드 풀의 정적 수는 애플리케이션 로직 복잡도, 서버 스펙 IO 리소스 상황에 따라 모두 다르다.
- 아파치 AB, JMeter, nGrinder






















































