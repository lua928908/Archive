# Optional 공부하기

이직 후 회사에서 스프링부트와 JPA를 사용하는데<br />
자바스크립트에 익숙했던 나에게 자바문법이 익숙하지 않고 생각처럼 되지않는 경우가 많았는데 가장 헷갈리는게 Optional 이였다.<br />
Optional을 학습해 보자

<br />

Optional의 특징
- 값이 있거나 없음을 표현한다.
- null을 대체한다.
- 자바 8 버전에서 추가 되었다.


```
if(sid != null && !sid.isEmpty()) {
    map.put("sid", sid);
}
if(utcTim != null && !utcTim.isEmpty()) {
    map.put("utcTim",  Double.parseDouble(utcTim));
}
if(rsltTTC != null && !rsltTTC.isEmpty()) {
    map.put("rsltTTC", Double.parseDouble(rsltTTC));
}
if(rsltDir != null && !rsltDir.isEmpty()) {
    map.put("rsltDir", Integer.parseInt(rsltDir));
}
if(rsltStt != null && !rsltStt.isEmpty()) {
    map.put("rsltStt", Integer.parseInt(rsltStt));
}
if(notiTyp != null && !notiTyp.isEmpty()) {
    map.put("notiTyp", Integer.parseInt(notiTyp));
}
if(hstId != null && !hstId.isEmpty()) {
    map.put("hstId", Integer.parseInt(hstId));
}
if(hstTyp != null && !hstTyp.isEmpty()) {
    map.put("hstTyp", Integer.parseInt(hstTyp));
}
```
위 내용 같은 IF지옥을 벗어날 수 있다.

---

<br />
<br />

## Optional 생성
- Optinal.of() -> null이 아닌 값을 생성할 때 사용 한다. (null이면 NullPointerException 에러 발생)
- Optional.ofNullable() -> null 일 수 있는 값을 생성할 때 사용 한다.
- Optional.empty() -> null이 아니라 빈값을 넣고 싶은 경우 empty()를 사용 한다.

<br />

## Optional 값 가져오기
- get()을 사용하면 된다 (값이 없는데 get()을 사용하면 NoSuchElementException 에러 발생)

<br />

값이 있는지 없는지 체크하기
- isPresent() -> 값이 있는지 확인
- isEmpty() -> 값이 없는지 확인한다. (자바11 이상부터 가능, isPresent가 false면 값이 없는 의미긴 하다.)

<br />

## 값이 있으면 동작 하기
- ifPresent() -> 값이 있으면 첫번째 인자로 넘어오는 콜백 함수를 실행 한다  -> opt.ifPresent(value -> doSome(value));
- ifPresentOrElse() -> 값이 있으면 첫번째 인자 콜백 함수 실행, 없으면 두번째 인자 함수 실행 -> opt.ifPresentOrElse((value) -> doSome(value), () -> doOther())

<br />

## 값이 없을 때 다른 값 사용
```
String myName = Optional.ofNullable(name).orElse("default name");
```
- orElse -> 값 자체를 리턴한다 -> opt.orElse("default")
- orElseGet -> 값 자체를 리턴하는데 값이 null인 경우에만 콜백함수를 실행 하고 값을 리턴한다. -> opt.orElseGet(() -> "default")
- or -> 값이 없을 때 옵셔널을 리턴한다. -> opt.or(() -> Optional.of("default"))

<br />

## 값이 없으면 익셉션을 발생하고 값이 있으면 값 리턴
- orElseThrow
```
Optional<Member> opt = repository.findById("id");
Member m = opt.orElseThrow(() -> new NoMemberException()) // 값이 없으면 Exception, 값 있으면 findById를 한 결과물 리턴
// opt에 값이 있다면 m에는 findById를 한 값이 담겨 있다.
```

<br />

## map - Optional에 담겨있는 값을 변경
- map을 사용해 Optional이 담긴 값을 하나씩 꺼내와 변형할 수 있다. (자바스크립트 에서도 map을 사용하면 새로운 array 리턴)
- map은 옵셔널을 리턴한다.
- 값이 없는 Optional에 map을 사용하면 아무 동작도 하지 않고 빈 Optional을 리턴한다.
```
// 각각 map을 돌려서 변수로 담아줄 수도 있고
Optional<Member> memOpt = ...;
Optional<LocalDate> birthOpt = memOpt.map(mem -> mem.getBirthday());
Optional<Integer> pdOpt = birthOpt.map(birth -> cal(birth));

// map을 체인으로 사용할 수도 있다
Optional<Integer> pdOpt2 =
    memOpt.map(mem -> mem.getBirthday())
          .map(birth -> cal(birth));
```

<br />

## filter
- 조건을 충족하면 값 그대로 리턴
- 충족하지 못 하면 빈 Optional 리턴
```
Optional<String> strOpt = ...;
Optional<String> filtered =
    strOpt.filter(str -> str.length() > 3)
          .ifPresent(str -> System.out.println(str));
```

---

<br />

## Optional을 사용하며 주의하기

Optional에 담길 값이 int, long, double이라면
Boxing/Unboxing이 발생하는 Optional<Integer>, Optional<Long>, Optional<Double>을 사용하지 말고,
OptionalInt, OptionalLong, OptionalDouble을 사용하자.

```
// 안 좋음
Optional<Integer> count = Optional.of(38);  // boxing 발생
for (int i = 0 ; i < count.get() ; i++) { ... }  // unboxing 발생

// 좋음
OptionalInt count = OptionalInt.of(38);  // boxing 발생 안 함
for (int i = 0 ; i < count.getAsInt() ; i++) { ... }  // unboxing 발생 안 함
```

















































































