---
title: Spring Boot Actuator 적용하기
catalog: true
date: 2017-04-23 13:47:26
subtitle: Spring Boot에서 제공해주는 관리 툴인 Actuator에 대해서 간략하게 알아본다
header-img: "../../../../2017/02/22/java-enum-abstract/Demo.png"
tags:
- spring
- spring boot
- actuator
catagories:
- Java
- Spring
---

## Spring Boot Actuator 적용하기

#### 개요
Spring boot용 헬스체크 및 다양한 기능을 제공하는 일종의 관리 툴

HTTP를 이용하여 어플리케이션에 대한 관리 및 모니터링 기능을 제공한다. 당연한 이야기이지만 기존 제공하는 서비스와 더불어서 적용이 가능함.
Remote Shell을 설정해 두면 SSH를 통한 접근도 가능하다

- [스프링 부트 문서 보기](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready.html#production-ready)
- 내장 톰캣을 이용해서 서비스 구동
- 다양한 형태의 jvm 메트릭 제공

문서가 너무 잘 나와있어서 보면서 쉽게 뚝딱 따라할 수 있다.

#### 설정법
pom.xml
```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-actuator</artifactId>
  </dependency>

  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-actuator-docs</artifactId>
  </dependency>
```
application.properties
```properties
#actuator
management.context-path=[YOUR_MANAGEMENT_CONTEXT_PATH]
management.port=[YOUR_PORT]
management.security.username=[YOUR_USERNAME]
management.security.password=[YOUR_PASSWORD]

endpoints.actuator.enabled=false
endpoints.auditevents.enabled=false
endpoints.dump.enabled=false
endpoints.flyway.enabled=false
endpoints.liquibase.enabled=false
endpoints.shutdown.enabled=false
endpoints.trace.enabled=false
endpoints.heapdump.enabled=false
endpoints.jolokia.enabled=false
endpoints.logfile.enabled=false

```
관리할 포트 및 endpoint설정, 미사용 기능 활성화|비활성화, security 설정 등.

WebSecurityConfig.java
```java
@EnableConfigurationProperties(ManagementProperties.class)
@Order(LOWEST_PRECEDENCE - 5)
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  //foo bar
  @Autowired
  public void registerGlobal(AuthenticationManagerBuilder auth, ManagementProperties managementProperties) throws Exception {
    auth
            .inMemoryAuthentication()
            .withUser(managementProperties.getUsername()).password(managementProperties.getPassword()).roles("ACTUATOR");
  }
}
```

ManagementProperites.java
```java
@Data
@Validated
@ConfigurationProperties("management.security")
public class ManagementProperties {

    /**
     * management username NotBlank
     */
    @NotBlank
    private String username;

    /**
     * management password NotBlank
     */
    @NotBlank
    private String password;
}
```

시큐리티 및 인증 정책은 사용자별로 적절한 정책을 구현하게 되어 있다.
위에서는 테스트를 위해 간단한 auth를 집어넣었다.



기존 시큐리티와 같이 사용하는 경우 `@Order`를 이용하여 순서를 지정해주어야 한다
자세한 내용은
https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-security-actuator
스프링 부트 공식 문서의 Actuator Security 참고


`spring-boot-actuator`
`spring-boot-actuator-docs` 이건 테스트용으로

### 제공하는 페이지 및 기능
공식 문서 참고
https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready-endpoints

- /actuator
- /auditevents
- /autoconfig
- /beans
- /configprops
- /dump
- /env
- /flyway
- /health
- /info
- /loggers
- /liquibase
- /metrics
- /mappings
- /shutdown
- /trace

Spring MVC를 사용하는 경우 아래 endpoint를 추가로 더 사용할 수 있다
- /docs
- /heapdump
- /jolokia
- /logfile

`/shutdown`과 같은 위험한 기능도 존재한다. 당연히 제공하는 엔드포인트 중 사용/미사용 설정이 가능하다

```properties
endpoints.shutdown.enabled=false
```
endpoints.[해당 ENDPOINT].enabled=true|false 형태로 정의하면 된다
이 밖에 자세한 설정 역시 공식 문서 참고