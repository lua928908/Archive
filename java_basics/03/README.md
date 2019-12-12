# 자바스크립트 개발자가 배워본 자바의 특징


<br />

## Interface

```
interface i{
    public void z();
}


class a implements i{
    public void z(){}
}
```

클래스대신 interface라는 키워드를 붙여 정의하면 implements를 통해 구현 한다.
구현된 것은 아무 것도 없는 밑그림만 있는 기본 설계도이며 인터페이스는 표준, 약속, 규칙 이다

예를들어 프론트개발자와 백엔드 개발자가 개발을할때 실제 api 혹은 데이터가 없는경우 프론트에서 dummy 데이터와 redux의 대략적인 모양새를
만들어 놓는데 이렇게 예상을 통해 작업을 구현하는 것처럼 자바에서는 interface를 통해 개발자들끼리 class와 method의 타입, 리턴값 등등
중요한 내용을 미리 정해놓을 수 있다. interface 에서 미리 기본적인 사항들이 정해져있는 상태에서 implements해서 클래스를 사용하면
추후에 여러 분야의 개발자들이 작업물을 하나로 합칠때 구조나 타입 등의 차이로 인해 문제가 생길것을 방지하기 위함 이다.
interface 내에 써있는 method는 public으로 만들어야 한다.

<br />

## Abstract

```
abstract class Animal {
	String animal_name;
	
	Animal(String name) {
		animal_name = name;
	}
	public abstract void cry();

	public abstract void behavior();

}

class Tiger extends Animal {
	
	public Tiger(String name) {
		super(name);
	}

	@Override
	public void cry() {
		// TODO Auto-generated method stub
		System.out.println("어흥");
	}

	@Override
	public void behavior() {
		// TODO Auto-generated method stub
		System.out.println("우측으로 빠르게 움직인다.");
	}

}

class Dog extends Animal {
	
	public Dog(String name) {
		super(name);
	}
	@Override
	public void cry() {
		// TODO Auto-generated method stub
		System.out.println("멍멍");
	}

	@Override
	public void behavior() {
		// TODO Auto-generated method stub
		System.out.println("왼쪽으로 느리게 달린다");
	}

}
```

<br />

## 어떨때 써야할까?

클래스는 일반 클래스와 추상 클래스로 나뉘는데(아마 구체적으로 정의하면 더 많을 것이다.) 추상 클래스는 클래스 내 '추상 메소드'가 하나 이상 포함되거나 abstract로 정의된 경우이고, 인터페이스는 모든 메소드가 추상 메소드인 경우다
두 키워드가 다소 비슷한 개념을 가지기도 하는데 abstract는 class와 별반 다르지않다. 그저 상속을 강제할 뿐이다.
abstract 메소드 안에 로직이 담길수 있다. 하지만 interface는 오롯이 뼈대이자 클래스의 명세만을 위해 쓰이는 것이기 때문에
별도의 로직을 가져서는 안되고 껍데기 즉, 만들어질 객체의 메소드명, 리턴타입, 접근제어자, 매개변수의 갯수와 타입 같은것만을
명시해야 한다. (여러 개발자의 개발과정에서 기초적인 found가 중구난방이 되는것을 막기위해)

**결론적으로 interface는 명세서의 개념이며 abstract는 상속해서 써라! 를 강제하는것이다. 상속을했으니 기능을 확장하는 것이 가능해진다.**

### 비슷해 보이는데 자바를 만든 사람들은 왜 두개를 따로 설계한 만든것일까?

이는 자바가 다중 상속을 지원하지 않기 때문이다. 다중 상속은 아래와 같이 여러 개의 슈퍼클래스를 두는 것을 의미한다.
```
class MyVehicle extends car, plane {

    @Override
    public void goTo(){
        super.drive();
    }

}
```
만약 car, plane 클래스 모두 drive()라는 메소드를 가지고 있다면, 어떤 메소드가 실행될까? 다중 상속의 모호함이다.
1편에서 자바의 특징에대해 이야기할때 자바는 c++과 다르게 다중상속을 허용하지않고 이런부분이 자바의 안정성을 높인다고 언급했다.

하지만 interface에서는 여러개의 인터페이스를 implements(구현) 하는것이 가능하다.
```
class car implements vehicle,engine

    @Override

    public void drive(){
        @doSomething
    }

}
```

얼핏보면 여러개를 상속하는것 처럼 보이지만 상속과 다형성이라는 키워드로 구분하면 될것같다.
