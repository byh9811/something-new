# Repository

---------------------

### `@Repository`
 - DAO Bean 객체라는 것을 명시하기 위해 사용하는 애너테이션
 - 그러나 JPA Repository를 구현하면 생략할 수 있음
 - 이유는 JpaRepositoriesRegistrar가 JpaRepository를 구현하는 모든 인터페이스를 빈으로 등록해주기 때문임
 - 그리고 스프링부트는 이 JpaRepositoriesRegistrar이 자동설정되어 있음

### `Projection`
- 인터페이스 기반의 Closed Projection을 사용할 때 반환 타입은 인터페이스의 형태임
- 그런데 구현체도 아닌 인터페이스의 getter 메서드를 사용할 수 있음
- 이유는 JPA Repository에서 쿼리의 결과로 구현체를 반환하는데, 인터페이스의 형태로 업캐스팅을 하기 때문임
- 결국 Virtual Method Invocation에 의해 구현체에 자동으로 오버라이딩된 메서드가 실행되는 것임