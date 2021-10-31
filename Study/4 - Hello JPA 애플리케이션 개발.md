# Hello JPA 애플리케이션 개발
---

## 객체와 테이블 생성 후 매핑

- `@Entity`: JPA가 관리할 객체
- `@Id`: 데이터베이스 PK와 매핑

```java
package hellojpa;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Member {

    @Id
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```sql
create table Member (
 id bigint not null,
 name varchar(255),
 primary key (id)
);
```

-> 데이터를 변경하는 모든 단위의 작업은 트랜잭션 내부에서 작업이 되어야 함

### 데이터 저장

```java
        transaction.begin();

        try {
            Member member = new Member();
            member.setId(1L);
            member.setName("Hello");

            entityManager.persist(member);

            transaction.commit();
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            entityManager.clear();
        }
```

### 데이터 조회

```java
        transaction.begin();

        try {
            Member member = entityManager.find(Member.class, 1L);
            System.out.println(member.getId());
            System.out.println(member.getName());

            entityManager.persist(member);

            transaction.commit();
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            entityManager.clear();
        }
```

### 데이터 삭제

```java
        transaction.begin();

        try {
            Member member = entityManager.find(Member.class, 1L);

            entityManager.remove(member);

            transaction.commit();
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            entityManager.clear();
        }
```

### 데이터 수정

```java
        transaction.begin();

        try {
            Member member = entityManager.find(Member.class, 1L);

            member.setName("HelloJPA");

            transaction.commit();
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            entityManager.clear();
        }
```

-> 데이터를 가져왔을때 JPA가 관리를 하는데 트랜잭션 커밋하는 시점에 변경사항을 체크하기 때문에 update 쿼리가 날라감

### 주의

- `엔티티 매니저 팩토리`는 하나만 생성해서 애플리케이션 전체에서 공유
- `엔티티 매니저`는 쓰레드간에 공유X (사용하고 버려야 함)
- `JPA의 모든 데이터 변경은 트랜잭션 안에서 실행`

## JPQL

> SQL을 추상화한 객체 지향 쿼리 언어

- JPA를 사용하면 엔티티 객체를 중심으로 개발
- 문제는 검색 쿼리
- 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능
- 애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검색 조건이 포함된 SQL이 필요

-> JPQL은 `엔티티 객체`를 대상으로 쿼리, SQL은 `데이터베이스 테이블`을 대상으로 쿼리