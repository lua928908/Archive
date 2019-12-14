# 자바스크립트 개발자가 배워본 자바의 특징


<br />

## 제네릭 (Generic)

자바 5 버전부터 제네릭이 추가되었다.

일반적으로 생각하는 데이터 타입은 int, char, double...같은 것들이 있다. 그런데 제네릭은 데이터 타입을 명시하지않고 선언했다가 인스턴스를 만들때 지정해주는것이다.
관용상 T라고 적어준다 제네릭 구현 방법은 아래와 같이 사용할 수 있다.
```
public class 클래스명<T>{ ... }
public interface 인터페이스명<T>{ ... }
```

그렇다면 왜써야될까?

### 1. 타입변환을 제거하여 성능을 높여준다.

Box.java
```
public class Box {
    private Object object;
    public Object get(){return object;}
    public void set(Object object){this.object = object;}
}
```

Main.java

```
public class Main {
 
    public static void main(String[] args) {
        Box box = new Box();
        box.set("제네릭");
        String str = box.get();//(String)box.get(); 캐스팅필요
        System.out.println(str);
    }
 
}
```
위 코드에서 에러가 발생할 것이다. Object를 String으로 변환해야 할 필요가 있기떄무니다.

매번 데이터타입을 강제로 캐스팅을 한다면 성능저하가 발생되기 때문에 인스턴스화시 타입을 지정해줘서 불필요한 강제타입변환을 막을 수 있다.

<br>

Box.java
```
public class Box<t> {
    private T t;
    public T get(){return t;}
    public void set(T t){this.t = t;}
}
```

Main.java
```
public class Main {
 
    public static void main(String[] args) {
        Box<string> box = new Box<string>();
        box.set("제네릭");
        String str = box.get();
        System.out.println(str);
    }
 
}
```

 인스턴스화 할때  `Box<String> box = new Box<String>();` 부분이 String으로 타입을 지정해주었다.


### 2. 코드의 재사용

제네릭을 사용하지 않는다면, `box.set(데이터타입);` 처럼 필요한 데이터타입에 맞는 새로운 클래스 또는 메서드를 작성해야한다.
제네릭을 통해 인스터스를 할때 타입을 지정하면 클래스 내부적으로 T 제네릭타입은 인스턴스 할때 지정한 타입으로 자동 재구성된다.

### 3. 엄격한 타입체크

```
package org.opentutorials.javatutorials.generic;
 
class StudentInfo
{
    public int grade;
    StudentInfo(int grade){ this.grade = grade; }
}
class EmployeeInfo{
    public int rank;
    EmployeeInfo(int rank){ this.rank = rank; }
}
class Person<t>{
    public T info;
    Person(T info){ this.info = info; }
}
public class GenericDemo {
    public static void main(String[] args) {
        Person<employeeinfo> p1 = new Person<employeeinfo>(new EmployeeInfo(1));
        EmployeeInfo ei1 = p1.info;
        System.out.println(ei1.rank); // 성공
          
        Person<string> p2 = new Person<string>("부장");
        String ei2 = p2.info;
        System.out.println(ei2.rank); // 컴파일 실패
    }
}
</employeeinfo></employeeinfo>
``` 

기존에 우리가 object로 작성한 코드는 모든 데이터 타입을 받아주기 위한 취지로 object를 썼지만, 그렇게 하면 개발자가 우리가 의도한 데이터타입을 사용하지 않고 위 코드처럼 우리가 의도하는 인스턴스 객체타입이 들어가야할 자리에 "부장" 이라는 String 타입이 들어가는 것을 막을 수 없다.
하지만 Generic(제네릭)을 사용하면 위와 같은 잠재성이 있는 에러를 런타임 사전에 에러체크 할 수 있다.


