## SpringBoot 개념잡기           start _ 2020-07-20

<br/>

## [**:book:** 스프링부트와 AWS로 혼자 구현하는 웹서비스 ](http://bit.ly/fr-springboot) 

<br/>

```
이동욱 개발자님의 도서를 참고하여 Springboot 개발 학습 및 실습할 예정입니다.
```

<br/>

## 💠  **개발환경**

- [x] #####  Java 8(JDK 1.8)

- [x] ##### Gradle 4.8 ~ Gradle 4.10.2

- [x] **JUnit4**

- [x] **lombok**

<br/>

## :heavy_check_mark: Chap.1 ( 인텔리제이로 스프링 부트 실행하기 )

### 1.4 Gradle 프로젝트를 스프링 부트 프로젝트로 변경하기

<br/>

```java
buildscript {
    ext {
        springBootVersion = '2.1.7.RELEASE'
    }
    repositories {
        mavenCentral()
        jcenter()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
// 스프링 부트의 의존성들을 관리해주는 플러그인
apply plugin: 'io.spring.dependency-management'

group 'com.jojoldu.book'
version '1.0-SNAPSHOT'

//각종 의존성들을 어떤 원격 저장소에서 받을지
repositories {
    mavenCentral()
}

// 프로젝트 개발에 필요한 의존성들을 선언하는 곳
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

<br/>

<br/>

<hr/>
# IntelliJ에서 깃과 깃허브 사용하기

**[ Ctrl + k ] Commit  &  [ Ctrl + Shift + K ] Push 진행**

<hr/>
<br/>

## :heavy_check_mark: Chap.2 ( 스프링 부트에서 테스트 코드 작성하기 )

- TDD와 단위 테스트는 다른 것이다.
  - 단위 테스트는 단지 기능 단위의 테스트 코드를 작성하는 것을 이야기 한다.
  - **빠른 피드백**, **자동 검증 가능**, **기존 기능 보호**

<br/>

- **Spring Boot**는 **내장 WAS**를 실행한다.
- **'언제 어디서나 같은 환경에서 스프링 부트를 배포'할 수 있기 때문**



### 2.2 Hello Controller 테스트 코드 작성하기

```java
package com.jojoldu.book.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정
// 항상 프로젝트 최상단에 위치해야 한다.
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

<br/>

```java
package com.jojoldu.book.springboot.web;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

// 컨트롤러를 JSON으로 반환하는 컨트롤러로 만들어 줍니다.
// 예전 ResponseBody를 각 메소드마다 선언했던 것을 한 번에 사용할 수 있게 해줌
@RestController
public class HelloController {

    // 예전 @RequestMapping(method = RequestMethod.GET) 과 같음
    @GetMapping("/hello")
    public String hello(){
        return "hello";
    }
    
    @GetMapping("/test")
    public String test(){
        return "test Success!";
    }
    
}
```

HelloController를 만들어준 뒤 간단한 테스트를 위해 "hello"를 return 해주는 코드를 작성하였습니다.

<br/>

```java
package com.jojoldu.book.springboot.web;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

// 테스트를 진행할 때, JUnit 내장 실행자 외에 다른 실행자를 실행시킴.
// 즉, 스프링 부트 테스트와 JUnit 사이에 연결자 역할
@RunWith(SpringRunner.class)
// Web(Spring MVC)에 집중할 수 있는 어노테이션, Controller 사용 가능
@WebMvcTest(controllers = HelloController.class)
public class HelloControllerTest {

    // 웹 API 테스트 할 때 사용
    // 스프링 MVC 테스트의 시작점
    @Autowired
    private MockMvc mvc;

    @Test
    public void hello가_리턴된다() throws Exception {
        String hello = "hello";

        mvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string(hello));
    }
    
    @Test
    public void test_재확인() throws Exception {
        String returnString = "test Success!";

        mvc.perform(get("/test"))
                .andExpect(status().isOk())
                .andExpect(content().string(returnString));
    }
}
```

<br/>

### 📝 자바 개발자들의 필수 라이브러리 롬복(Lombok)

- 롬복은 자바 개발할 때 자주 사용하는 코드 Getter, Setter, 기본생성자, toString 등을 어노테이션으로 자동 생성해 줍니다.

  <br/>

:one: build.gradle에 **compile('org.projectlombok:lombok')** 코드를 추가해줍니다.

:two: [ **Ctrl + Shift + A** ] -> **plugins** 실행​ 후 "**lombok**" 설치

:three: **Settings** -> **Build, Executin, Deployment** -> ​**Annotation Processors** -> **Enable annotation processing** 활성화

<br/>

### 2.4 Hello Controller 코드 롬복으로 전환하기

```java
package com.jojoldu.book.springboot.web.dto;


import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
// 선언된 모든 final 필드가 포함된 생성자를 생성해 줍니다.
@RequiredArgsConstructor
public class HelloResponseDto {
    
    private final String name;
    private final int amount;

}
```

<br/>

```java
package com.jojoldu.book.springboot.web.dto;

import junit.framework.TestCase;
import org.junit.Test;

// 테스트 검증 라이브러리의 검증 메소드, isEqualTo 사용 가능
import static org.assertj.core.api.Assertions.assertThat;

public class HelloResponseDtoTest extends TestCase {

    @Test
    public void 롬복_기능_테스트() {
        //given
        String name = "test";
        int amount = 1000;

        //when
        HelloResponseDto dto = new HelloResponseDto(name, amount);

        //then
        // isEqualTo는 assertj의 동등 비교 메소드
        assertThat(dto.getName()).isEqualTo(name);
        assertThat(dto.getAmount()).isEqualTo(amount);
    }
}
```

<br/>

#### > JUnit의 기본 assertThat이 아닌 assertj의 assertThat을 사용함

- **CoreMatchers와 달리 추가적으로 라이브러리가 필요하지 않습니다.**
  - Junit의 assertThat을 쓰게 되면 is()와 같이 CoreMatchers 라이브러리가 필요함
- **자동완성이 좀 더 확실하게 지원됩니다.**



**HelloController**에 ResponseDto를 사용하는 코드 추가

```java
	// RequestParam
	// => 외부에서 API로 넘긴 파라미터를 가져오는 어노테이션
    @GetMapping("/hello/dto")
    public HelloResponseDto helloDto(@RequestParam("name") String name, @RequestParam("amount") int amount) {
        return new HelloResponseDto(name, amount);
    }
```

<br/>

추가된 API를 테스트하는 코드를 **HelloControllerTest**에 추가

```java
    @Test
    public void helloDto가_리턴된다() throws Exception {
        String name = "hello";
        int amount = 1000;

        mvc.perform(get("/hello/dto")
                    .param("name", name)
                    .param("amount", String.valueOf(amount)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name", is(name)))
                .andExpect(jsonPath("$.amount", is(amount)));
    }
```

<br/>

# Mybatis  VS  JPA

- Mybatis는 ORM이 아닌 SQL Mapper이다.
  - ORM은 객체를 매핑하는 것이고, SQL Mapper는 쿼리를 매핑하는 것

- **DB**와 **객체지향** 사이에서 일어난 **패러다임의 불일치**
  - User와 Group의 부모 - 자식 관계를 통해 파악해보자

```java
// user와 group은 부모 - 자식 관계
User user = findUser();
Group group = user.getGroup();

// 여기에 데이터베이스가 추가된다면 ?
User user = userDao.findUser();
Group group = groupDao.findGroup(user.getGroupId());
// User 따로, Group 따로 조회하게 됩니다.
// 즉, 상속이나 1:N 등 객체 모델링을 데이터베이스로는 구현할 수 없습니다 !

//////////////////////////////////////////////////
// 위와 같은 문제를 해결하기 위해
// 개발자는 객체지향 프로그래밍을 하고, JPA가 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행
// -> 개발자는 더이상 SQL 종속적인 개발을 하지 않아도 된다.
```

<br/>

### 3.2 프로젝트에 Spring Data Jpa 적용하기

먼저 build.gradle에 다음과 같이 org.springframework.boot:spring-boot-starter-data-jpa와 com.h2database:h2 의존성들을 등록합니다.

```java
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    compile('org.projectlombok:lombok')
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    compile('com.h2database:h2')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

- spring-boot-starter-data-jpa
  - 스프링 부트용 Spring Data Jpa 추상화 라이브러리
  - 스프링 부트 버전에 맞춰 자동으로 JPA 관련 라이브러리들의 버전을 관리
- h2
  - 인메모리 관계형 데이터베이스
  - 별도의 설치가 필요 없이 프로젝트 의존성만으로 관리할 수 있습니다.
  - 애플리케이션 재시작할 때마다 초기화되어 테스트 용도로 많이 사용
  - JPA의 테스트, 로컬 환경에서의 구동에서 사용할 예정

<br/>

## 👨‍💻 Spring Data Jpa를 사용하여 게시판 만들기

기본 객체들을 담을 도메인 **domain** 패키지를 만들어 주었습니다.

- **도메인**이란 게시글, 댓글, 회원, 결제 등 소프트웨어에 대한 요구사항 혹은 문제 영역이라고 생각하면 됩니다.

<br/>

1. domain 패키지에 **posts 패키지**와 **Posts 클래스**를 만들어줍니다.

```java
package com.jojoldu.book.springboot.domain.posts;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class Posts {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 500, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

    @Builder
    public Posts(String title, String content, String author){
        this.title = title;
        this.content = content;
        this.author = author;
    }
}
```

<br/>

자바빈 규약을 생각하면서 **getter/setter**를 무작정 생성하는 경우가 있습니다.

이렇게 되면 해당 클래스의 인스턴스 값들이 언제 어디서 변해야 하는지 코드상으로

명확히 구분할 수가 없어 차후 기능 변경 시 복잡해질 수 있습니다.

<br/>

#####  Posts 클래스에는 Setter 메소드가 없다는 특징이 있습니다.

#####  Entity 클래스에서는 절대 Setter 메소드를 만들지 않습니다.

<br/>

```java
# 주문 취소 메서드를 만들 때

    // 잘못된 사용 예
    public class Order{
        public void setStatus(boolean status){
            this.status = status;
        }
    }

	public void 주문서비스의_취소이벤트(){
        order.setStatus(false);
    }

	
	// 올바른 사용 예
	public class Order{
        public void cancelOrder(){
            this.status = false;
        }
    }

	public void 주문서비스의_취소이벤트(){
        order.cancelOrder();
    }
```









# JPA 어노테이션

```java
@Entity - 테이블과 링크될 클래스임을 나타낸다. 
    	- 카멜케이스 매칭(UserImage.java-> user_image table)
@Table - DB와 매핑될 클래스 정보
@Column - 매핑할 테이블 컬럼명을 name 속성으로 매핑하는 용도
    	- name 생략 시, 필드의 이름을 따름
```



# Lombok 어노테이션

```java
@Getter - 클래스 내 모든 필드의 Getter 메소드를 자동으로 생성
@Setter - 클래스 내 모든 필드의 Setter 메소드를 자동으로 생성
@NoArgsConstructor - 기본 생성자를 자동으로 추가해줍니다.
@AllArgsConstructor - 모든 속성에 대해서 생성자를 만들어 냅니다.
@ToString - toString() 메소드를 생성합니다. 
    @ToString(exclude={"제외할 값"}) 처럼 원하지 않는 속성은 제외할 수 있습니다.
    
@Data - @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor 어노테이션의 묶음 입니다.
```


