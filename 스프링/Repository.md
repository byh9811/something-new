# Repository

---------------------

### `@Repository`
 - DAO Bean 객체라는 것을 명시하기 위해 사용하는 애너테이션
 - 그러나 JPA Repository를 구현하면 생략할 수 있음
 - 이유는 JpaRepositoriesRegistrar가 JpaRepository를 구현하는 모든 인터페이스를 빈으로 등록해주기 때문임
 - 그리고 스프링부트는 이 JpaRepositoriesRegistrar이 자동설정되어 있음