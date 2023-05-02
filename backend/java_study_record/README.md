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

### singleton 패턴
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

### Strategy 패턴
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

## State 패턴
strategy 패턴과 전략패턴은 동일한 틀 안에 있는 특정 작업 방식, mode를 변경 할 때 사용된다.  
state 패턴은 tv가 꺼져 있을때 켜지고/꺼지는 버튼처럼 특정 상태마다 다르게 해야할 일을 모듈화해서 지정해둘 때 사용된다.


## Command 패턴
strategy 패턴과 비슷하다 전략 패턴은 같은 일을 하되 알고리즘이나 방식 자체가 변경되는 것이라고 볼 수 있고 command 패턴은 하는일 자체가 변경되는 것이다.
command 패턴을 구현할 때 strategy 패턴처럼 알고리즘을 갈아끼우기도 하고 여러 명령들을 목록으로 실어서 차례대로 실행시킬 수도 있다.  

MyProgram.java
```java
package command;

public class MyProgram {

  public static void main(String[] args) {
    RobotKit robotKit = new RobotKit();

    robotKit.addCommand(new MoveForwardCommand(2));
    robotKit.addCommand(new TurnCommand(Robot.Direction.LEFT));
    robotKit.addCommand(new MoveForwardCommand(1));
    robotKit.addCommand(new TurnCommand(Robot.Direction.RIGHT));
    robotKit.addCommand(new PickupCommand());

    robotKit.start();

    // 2 칸 전진
    // 왼쪽으로 방향전환
    // 1 칸 전진
    // 오른쪽으로 방향전환
    // 앞의 물건 집어들기
  }
}
```

Robot.java
```java
package command;

public class Robot {
  public enum Direction { LEFT, RIGHT }

  public void moveForward (int space) {
    System.out.println(space + " 칸 전진");
  }

  public void turn (Direction _direction) {
    System.out.println(
      (_direction == Direction.LEFT ? "왼쪽" : "오른쪽") + "으로 방향전환"
    );
  }

  public void pickup () {
    System.out.println("앞의 물건 집어들기");
  }
}
```

Command.java
```java
package command;

abstract class Command {
  protected Robot robot;

  public void setRobot (Robot _robot) {
    this.robot = _robot;
  }
  public abstract void execute ();
}

class MoveForwardCommand extends Command {
  int space;
  public MoveForwardCommand (int _space) { 
    space = _space; 
  }
  public void execute () {
    robot.moveForward(space);
  }
}

class TurnCommand extends Command {
  Robot.Direction direction;
  public TurnCommand (Robot.Direction _direction) {
    direction = _direction;
  }
  public void execute () {
    robot.turn(direction);
  }
}

class PickupCommand extends Command {
  public void execute () {
    robot.pickup();
  }
}
```

RobotKit.java
```java
package command;

import java.util.ArrayList;

public class RobotKit {
  private Robot robot = new Robot();
  private ArrayList<Command> commands = new ArrayList<Command>();

  public void addCommand (Command command) {
    commands.add(command);
  }
  public void start () {
    for (Command command : commands) {
      command.setRobot(robot);
      command.execute();
    }
  }
}
```

## Adapter 패턴
strategy 패턴에서 사용했던 예제를 떠올려 보자, 전체검색, 이미지검색 등의 여러 검색 전략이 있다. 그런데 외부 API를 사용해서 동영상 검색을 추가한다고
가정 해보자, 이 검색 방식은 다른 회사에서 개발한 기능이기 때문에 우리가 사용하던 것과는 메서드형식, 타입, 인터페이스이름도 다르다. 그렇다고 외부 API를 위해
새 인터페이스를 만드는 것도 효율적이지 않을 것이다 이럴때 Adapter 패턴을 사용하면 된다.

FindAlgorithm.java
```java
package adapter.search;

interface FindAlgorithm {
  public void find (boolean global);
}

class FindMovieAlgorithm implements FindAlgorithm {
  public void find (boolean global) {
      System.out.println(
        "find movie" + (global ? " globally" : "")
      );
      // 동영상검색하는 코드
      // ...
      // ...
  }
}
```

SearchStrategy.java
```java
//...

class SearchFindAdapter implements SearchStrategy {
  private FindAlgorithm findAlgorithm;

  public SearchFindAdapter (FindAlgorithm _findAlgorithm) {
    findAlgorithm = _findAlgorithm;
  }
  public void search () {
    findAlgorithm.find(true);
  }
}
```

MyProgram.java
```java
  //...

  public void setModeMovie () {
    searchButton.setSearchStrategy(
      new SearchFindAdapter(new FindMovieAltorithm())
    );
  }
```

## Proxy 패턴

프로그램에서 사용되는 클래스들 중에서 네트워크를 사용해 다운로드 해야 하거나, 메모리를 많이 차지하는 등의 이유로
객체로 여럿 생성하기 부담스러운 경우가 있다, 이럴때 그 클래스의 proxy(대리자) 역할을 하는 클래스를 따로 만들어서 가벼운 행동은 proxy를 사용하고
무거운 작업을 해야 할 때 실제 클래스를 사용하는 패턴이다. 

MyProgram.java
```java
package proxy;

import java.util.ArrayList;

public class MyProgram {
  public static void main(String[] args) {
    ArrayList<Thumbnail> thumbnails = new ArrayList<Thumbnail>();

    thumbnails.add(new ProxyThumbnail("Git 강좌", "/git.mp4"));
    thumbnails.add(new ProxyThumbnail("Rest API란?", "/rest-api.mp4"));
    thumbnails.add(new ProxyThumbnail("도커 사용법", "/docker.mp4"));
    thumbnails.add(new ProxyThumbnail("객체지향 프로그래밍", "/oodp.mp4"));
    thumbnails.add(new ProxyThumbnail("블록체인의 원리", "/blockchain.mp4"));

    for (Thumbnail thumbnail : thumbnails) {
      thumbnail.showTitle();
    }
    // 제목:Git 강좌
    // 제목:Rest API란?
    // 제목:도커 사용법
    // 제목:객체지향 프로그래밍
    // 제목:블록체인의 원리

    thumbnails.get(2).showPreview();
    thumbnails.get(2).showPreview();
    thumbnails.get(4).showPreview();
    thumbnails.get(1).showPreview();

    // /docker.mp4로부터 도커 사용법의 영상 데이터 다운
    // 도커 사용법의 프리뷰 재생
    // 도커 사용법의 프리뷰 재생
    // /blockchain.mp4로부터 블록체인의 원리의 영상 데이터 다운
    // 블록체인의 원리의 프리뷰 재생
    // /rest-api.mp4로부터 Rest API란?의 영상 데이터 다운
    // Rest API란?의 프리뷰 재생
  }
}
```

Thumbnail.java
```java
package proxy;

interface Thumbnail {
  public void showTitle ();
  public void showPreview ();
}

class RealThumbnail implements Thumbnail {
  private String title;
  private String movieUrl;

  public RealThumbnail (String _title, String _movieUrl) {
    title = _title;
    movieUrl = _movieUrl;

    // URL로부터 영상을 다운받는 작업 - 시간 소모 
    System.out.println(movieUrl + "로부터 " + title + "의 영상 데이터 다운");
  }
  public void showTitle () {
    System.out.println("제목:" + title);
  }
  public void showPreview () {
    System.out.println(title + "의 프리뷰 재생");
  }
}

class ProxyThumbnail implements Thumbnail {
  private String title;
  private String movieUrl;

  private RealThumbnail realThumbnail;

  public ProxyThumbnail (String _title, String _movieUrl) {
    title = _title;
    movieUrl = _movieUrl;
  }

  public void showTitle () {
    System.out.println("제목:" + title);
  }

  public void showPreview () {
    if (realThumbnail == null) {
      realThumbnail = new RealThumbnail(title, movieUrl);
    }
    realThumbnail.showPreview();
  }
}
```

#### 출처
* [얄코 사이트](https://www.yalco.kr/29_oodp_1/)
* [얄코 유튜브 강의](https://www.youtube.com/watch?v=lJES5TQTTWE)