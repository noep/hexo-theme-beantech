---
title: "Java Enum과 JPA"
catalog: false
date: 2017-02-22 22:22:22
subtitle: "Enum을 스프링 부트와 JPA를 쓸 때 함께 쓰는 방법에 대해 간단하게 정리"
tags:
- java
- enum
- jpa
- spring
catagories:
- Java
header-img: "Demo.png"
---

### Java Enum과 JPA
java 1.5버전부터(상당히 오래전부터) 추가된 열거형 enum에 대해서 다룬다.

```
public static final int JANUARY = 1;
public static final int FEBRUARY = 2;
```
같은 형태의 것들을 좀 더 간결하게 다룰 수 있어 코드 가독성을 향상시칸다.

```
public enum TestEnum {
    ENUM_NUMBER_ZERO("첫 번째 열거형");
    ENUM_NUMBER_ONE("두 번째 열거형");
    
    private String message;

    PushConfirm(String message) {
        this.message = message;
    }

    public String getMessage() {
        return this.message;
    }
}

@Test
public void test() {

    TestEnum enums = TestEnum.ENUM_NUMBER_ZERO;

    log.info("{}",enums.toString());
    log.info("{}",enums.name());
    log.info("{}",enums);
    log.info("{}",enums.getMessage());
    log.info("{}",enums.ordinal());
}
```

실제로 Spring의 HttpStatus를 까보면 좀 더 복잡하지만 대충 이런 구조로 되어있다.  
안에 private 필드를 추가한 뒤 생성자 형태로 입력받아서 사용이 가능하다.  
커스텀 필드 추가에 따른 getter를 구현해놓으면 편하게 내용물을 꺼내쓸 수 있다.

아래는 Test 코드에 대한 결과 로그이다
```
10:16:43.114 [main] INFO - ENUM_NUMBER_ZERO
10:16:43.115 [main] INFO - ENUM_NUMBER_ZERO
10:16:43.115 [main] INFO - ENUM_NUMBER_ZERO
10:16:43.115 [main] INFO - 첫 번째 열거형
10:16:43.115 [main] INFO - 0
```

### Enum은 Jpa에서 엔티티 안에 있는 Column으로도 맵핑이 가능하다. 

그냥 Enum을 쓰면 Ordinal 타입으로 인식한다.(즉 숫자 형태로 인식함)  
이걸 String 형태의 Enum으로 쓰려면

```java
@Enumerated(EnumType.STRING)
```
어노테이션을 이용해서 표시해줘야 한다.

컬럼 어노테이션을 어떻게 정의하냐에 따라 다르게 쓰이는데
1. mysql enum을 쓰는 경우 아래에 한줄을 더 해줘서 컬럼 타입을 명시해줘야 함
  이 뒤에 뭔가가 붙게 되는 경우 enum추가할 때마다 번거로워지므로 딱 enum까지가 적당 (이건 상황에 따라서 다르게 쓰면 될듯)

    ```java
    @Column(columnDefinition = "enum")
    ```
  
2. 안해주면 그냥 VARCAHR로 잡아놓는 것 같음(JPA기본전략인듯) 이때는 컬럼 길이정도만 정해주면 됨
    ```java
    @Column(length = 5)
    ```

결론적으로 엔티티 선언할 때
```java
@Enumerated(EnumType.STRING)  
@Column(columnDefinition = "enum")  
private TestEnum enums; //-- 적당한 주석  
```

### 또한 컨트롤러 단에서 인자로 받을 수도 있다. 

Ordinal타입, String 타입 할것없이 다 입력이 된다 심지어 
- 0, 
- "0",
- "ENUM_NUMBER_ZERO"  
위 3개를 같은 enum으로 인식한다 
이걸 분류하려면 뭘 써야될지는 아직 안해봄

Enum의 Serialization에 관련하여 아래와 같은 기사를 찾았다.   
[custom-json-serialization-for-enums-using-jackson](http://chrisjordan.ca/post/50865405944/custom-json-serialization-for-enums-using-jackson)  
좀 더 연구해보아야 할 듯 하다만
간단하게 말해 custom하게 jackson deserializer를 구현해 주어야 하는 것 같다.  

