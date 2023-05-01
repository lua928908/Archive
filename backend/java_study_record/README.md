# 혼자 공부하며 JAVA 기록하기

> * 자바를 공부하며 학습한 지식을 간략하게 기록하기 위한 문서 입니다.
> * 혼자 공부하며 내 방식대로 기록한 글이기에 설명이 왜곡되거나 부정확한 부분이 많습니다. 

<br>

## 리플렉션

모든 클래스의 정보는 JVM 안에 있는 클래스로더가 읽어와서 메모리의 어딘가에 두는 것이다. 클래스 로더가 읽어온 클래스 정보가 곧 거울에 비친 모습과 같은 것이다.
이런 정보를 가지고 여러가지 여러가지 행동을 할 수 있다 예를들어 어노테이션이 붙어 있으면 특정 행위를 실행 한다거나 테스트를 할 때 추가 행위를 한다거나 특정 네이밍을 검색해 치환 한다거나
모두 리플렉션 덕분에 가능한 행위다. 무긍무진하게 활용될 수 있다.

```java
// 클래스 정보를 가져온다. (레퍼런스를 가져온다)
Class<?> aClass = Class.forName("me.whiteship.hello.ChineseHelloService");

// constructor를 가져온다. aClass에서 바로 newInstance()를 사용하지 말고 컨스트럭터를 가져와서 인스턴스를 생성함.
// getDeclaredMathd(), getDeclaredFields() 등으로 객체의 메소드나 필드정보를 가져올 수 있다, 심지어 private도 가능
Contructor<?> constructor = aClass.getConstructor();

// instance를 만들고 타입을 변환한다.
HelloService helloService = (HelloService) constructor.newInstance();

System.out.println(helloService.hello());
```

## 디자인 패턴

### singleton
프로그램 전체에서 1개의 인스턴스(객체)에만 접근할 수 있도록 만드는 패턴, 환경설정과 같이 어플리케이션이나 프로그램 전체에 1개의 객체만 필요할 때 사용하는데 멀티스레드 환경에서 오류가 발생할 소지가 있다.

MyProgram.java
```java
package singleton.after;

public class MyProgram {
  public static void main (String[] args) {
    new FirstPage().setAndPrintSettings();
    new SecondPage().printSettings();
  }
}
```

Settings.java
```java
package singleton.after;

public class Settings {

  private Settings () {};
  private static Settings settings = null;

  public static Settings getSettings () {
    if (settings == null) {
      settings = new Settings();
    }
    return settings;
  }

  private boolean darkMode = false;
  private int fontSize = 13;

  public boolean getDarkMode () { return darkMode; }
  public int getFontSize () { return fontSize; }

  public void setDarkMode (boolean _darkMode) { 
    darkMode = _darkMode; }
  public void setFontSize (int _fontSize) { 
    fontSize = _fontSize; }
}
```

FirstPage.java
```java
package singleton.after;

public class FirstPage {
  private Settings settings = Settings.getSettings();

  public void setAndPrintSettings () {
    settings.setDarkMode(true);
    settings.setFontSize(15);
    System.out.println(settings.getDarkMode() + " " + settings.getFontSize());
  }
}
```

SecondPage.java
```java
package singleton.after;

public class SecondPage {
  private Settings settings = Settings.getSettings();

  public void printSettings () {
    System.out.println(settings.getDarkMode() + " " + settings.getFontSize());
  }
}
```

### strategy
[전체, 이미지, 뉴스, 지도] 검색이 있다고 치자, 사용자가 어떤 검색을 눌렀지에 따라 if,else 로 분기처리를 해서 onClick 메서드에 코드를 작성할텐데 기능이 추가되거나 수정되면 그때마다 onClick 메서드가 변경되어야 하는데 이런 설계는 우아하지 않다. 클래스마다 역할지정을 뚜렷하게 해서 모듈화된 소프트웨어를 구축하는 객체지향의 철학과도 맞지 않는다. if, else로 각 역할을 다 써주는게 아니라 SearchStrategy 라는 인터페이스를 생성, 각 버튼마다 어떤 검색을 할건지 [전체검색 객체, 이미지검색 객체, 뉴스검색 객체, 지도검색 객체]를 생성해서 관리해주면 된다.

Main.java
```java
package strategy.after;

public class Main {
  public static void main(String[] args) {
    MyProgram myProgram = new MyProgram();
    myProgram.testProgram();
  }
}
```

MyProgram.java
```java
package strategy.after;

public class MyProgram {
  private SearchButton searchButton = new SearchButton(this);

  public void setModeAll () { 
    searchButton.setSearchStrategy(new SearchStratagyAll());
  }
  public void setModeImage () {
    searchButton.setSearchStrategy(new SearchStratagyImage());
  }
  public void setModeNews () {
    searchButton.setSearchStrategy(new SearchStratagyNews());
  }
  public void setModeMap () {
    searchButton.setSearchStrategy(new SearchStratagyMap());
  }

  public void testProgram () {
    searchButton.onClick();   // "SEARCH ALL" 출력
    setModeImage();           // 이미지검색 모드로
    searchButton.onClick();   // "SEARCH IMAGE" 출력
    setModeNews();            // 뉴스검색 모드로
    searchButton.onClick();   // "SEARCH NEWS" 출력
    setModeMap();             // 지도검색 모드로
    searchButton.onClick();   // "SEARCH MAP" 출력
  }
}
```

SearchStrategy.java
```java
package strategy.after;

interface SearchStrategy {
  public void search ();
}

class SearchStratagyAll implements SearchStrategy {
  public void search () {
      System.out.println("SEARCH ALL");
      // 전체검색하는 코드
  }
}

class SearchStratagyImage implements SearchStrategy {
  public void search () {
      System.out.println("SEARCH IMAGE");
      // 이미지검색하는 코드
  }
}

class SearchStratagyNews implements SearchStrategy {
  public void search () {
      System.out.println("SEARCH NEWS");
      // 뉴스검색하는 코드
  }
}

class SearchStratagyMap implements SearchStrategy {
  public void search () {
      System.out.println("SEARCH MAP");
      // 지도검색하는 코드
  }
}
```

SearchButton.java
```java
package strategy.after;

public class SearchButton {

  private MyProgram myProgram;

  public SearchButton (MyProgram _myProgram) {
    myProgram = _myProgram;
  }

  private SearchStrategy searchStrategy = new SearchStratagyAll();
  
  public void setSearchStrategy (SearchStrategy _searchStrategy) {
    searchStrategy = _searchStrategy;
  }

  public void onClick () {
    searchStrategy.search();
  }
}
```

state 패턴: stratege 패턴과 유사하다