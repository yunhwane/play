
## DDL 설정 옵션 
```
spring:
  jpa:
    hibernate:
        ddl-auto: 
```
- create : 애플리케이션 실행 시 테이블 제거 및 다시 생성
- create-drop : 애플리케이션 실행 시 종료 시점에 테이블 제거, 다시 생성
- update : 애플리케이션 실행 시 변경점만 반영
- validate : 현재 테이블 정보가 entity에 정의된 내용과 일치하는지 확인, 만약 다르면 컴파일 x
- none : 자동 생성 옵션 x


## SQL 조회 옵션
```
spring:
  jpa:
    properties:
        show_sql: true
        format_sql: true
```

- sql 쿼리 조회하는 옵션, format_sql 은 정돈된 쿼리가 나감.

```
logging:
  level:
    org:
      hibernate:
        type:
          descriptor:
            sql: trace

```
- 추적 옵션
