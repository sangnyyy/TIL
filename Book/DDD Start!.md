# DDD Start!
> 내맘대로 정리한 DDD Start!

## Chapter 01 - 도메인 모델 시작

* 도메인
  * 소프트웨어로 해결하고자 하는 문제의 영역
  * ex) 온라인 서점 = 구현해야 할 소프트웨어의 대상
* 도메인은 다시 여러개의 하위 도메인으로 구성될 수 있다
    <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/%ED%95%98%EC%9C%84%EB%8F%84%EB%A9%94%EC%9D%B8.jpg?raw=true" width=300 height=300/>
* 도메인 모델
  * 특정 도메인을 개념적으로 표현한 것(개념모델 O, 구현모델 X)
  * 도메인 모델을 사용하여, 여러 관계자들이 동일한 모습으로 도메인을 이해할 수 있다.
  * 도메인을 이해하는 데 도움이 된다면 어떤 표현 방식도 상관 없다.
    * ex) 상태 다이어그램, 클래스 다이어그램 ...
* 도메인 모델 패턴
  * 도메인 계층을 객체지향 기법으로 구현하는 패턴
    * 여기서, 도메인 모델은 도메인 계층 구현 시 사용하는 객체모델을 의미
  * 다른코드에 영향을 덜 주며 변경내역을 모델에 반영할 수 있다.
  * 도메인 모델 도출
    * 핵심 구성요소, 규칙, 기능을 찾는다.
    * 이 과정은 요구사항에서 출발
* 엔티티와 밸류
  * 엔티티
    * 식별자를 갖는다.
    * 자신의 라이프사이클이 존재
  * 밸류
    * 도메인 객체의 속성을 표현
    * 의미를 명확하게 하기 위해 사용하는 경우도 존재.
      * ex) Money 클래스

## Chapter 02 - 아키텍처 개요

* 표현, 응용, 도메인, 인프라스트럭처
  * 표현(UI) 영역
    * 사용자의 요청을 받아서 응용 영역에 전달 후 처리 결과를 다시 돌려주는 역할
  * 응용 영역
    * 로직을 수행하기 보단 도메인에 로직 수행을 위임하는 역할
    * 트랜잭션 관리의 역할
  * 도메인 영역
    * 도메인 모델을 구현
    * 도메인의 핵심 로직 구현
  * 인프라스트럭처 영역
    * 표현, 응용, 도메인 영역을 지원
    * 구현 기술에 대한 것을 다룬다.
      * ex) ORM, SMTP, MQ 등..
    * 논리적인 개념 보다는 실제 구현
* DIP
  * 인프라스트럭처 계층에 의존하면 테스트의 어려움, 기능 확장의 어려움
  * DIP는 저수준 모듈이 고수준 모듈에 의존하도록 한다. 그 비밀은 추상화 한 인터페이스에 있다.
    <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/DIP.jpg?raw=true" width=400 height=300/>
  * 저수준 모듈만 바꿔 끼우면 된다. = 확장성
  * Mock 객체를 끼워서 저수준 모듈이 구현되지 않았음에도 테스트를 진행할 수 있다.
  * DIP가 적용된 최종 구조는 다음과 같다.
    <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/DIP2.jpg?raw=true" width=400 height=300/>
* 애그리거트
  * 관련 객체(엔티티, 벨류)를 하나로 묶은 군집
    * ex) 주문 도메인 = {주문, 배송, 주문자, 주문목록, 결제금액 ....}
  * 루트 엔티티를 갖는다.
    * 이를 통해, 간접적으로 다른 엔티티나 벨류 객체에 접근
      <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/aggregate.jpg?raw=true" width=400 height=300/>

## Chapter 03 - 애그리거트

* 애그리거트
  * 복잡한 도메인을 이해하고 관리하기 쉬운 단위로 만들기 위해 상위 수준에서 조망할 수 있는 방법
  * 일관성을 관리하는 기준
  * 애그리거트에 속하는 객체는 유사하거나 동일한 라이프사이클을 갖는다.
    * 대부분 함께 생성되고, 함께 제거된다.
  * 다른 애그리거트를 관리하지 않는다.
    * ex) 상품과 리뷰
      <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/aggregate2.jpg?raw=true" width=400 height=200/>
  * 애그리거트 루트
    * 군집에 속한 객체가 일관된 상태를 갖기 위해 관리할 주체
    * 다른 객체가 애그리거트 내 객체를 직접 변경하면 안된다. 오로지, 애그리거트 루트를 통해 직/간접적 변경(최대한 불변객체로..)
* 트랜잭션
  * 범위는 작을수록 좋다.
  * 한 트랜잭션에서 한 애그리거트만 수정해야 한다.
    * 전체 처리량을 떨어트리고, 트랜잭션 충돌이 발생할 가능성이 높아지기 때문.
    * 또한, 이는 한 애그리거트에서 다른 애그리거트를 변경하지 않는다는 것을 의미한다. 애그리거트 간 결합도가 높아지는 결과때문.
  * 불가피하게 두개이상의 애그리거트 수정이 필요 시, 응용 서비스에서 이를 구현한다.
    * 혹은 도메인 이벤트를 사용해서 비동기로 다른 애그리거트 상태를 변경
* 리포지터리
  * 애그리거트는 개념적으로 하나이다. 따라서, 리포지터리는 애그리거트 전체를 저장소에 영속화해야 한다.
  * 애그리거트의 상태가 변경되면 모든 변경을 원자적으로 저장소에 반영해야 한다.

## Chapter 04 - 리포지터리와 모델 구현

* AttributeConverter를 이용한 밸류 매핑 처리
    ```java
    public interface AttributeConverter<X, Y> {
        public Y convertToDatabaseColumn (X attribute);
        public X convertToEntityAttribute (Y dbData);
    }
    ```
    ```java
    public class Order {  
        @Column(name = "total_amounts")
        @Convert(converter = MoneyConverter.class)
        private Money totalAmounts;
    }
    ```

* 밸류 컬렉션을 별도 테이블로 매핑
  * @ElementCollection과 @CollectionTable 사용
  <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/valuecollection.jpg?raw=true" width=400 height=200/>
  
    ```java
    @Entity
    @Table(name = "purchase_order")
    public class Order{
        @ElementCollection
        @CollectionTable(name = "order_line"),
                        joinColumns = @JoinColumn(name = "order_number"))
        @OrderColumn(name = "line_idx")
        private List<OrderLine> orderLines;
    }
    ```

* 별도 테이블에 저장하는 밸류 매핑
  * @SecondaryTable과 @AttributeOverride를 사용
  <br/><img src="https://github.com/sangnyyy/TIL/blob/master/images/valuemapping.jpg?raw=true" width=400 height=200/>
    ```java
    @Entity
    @SecondaryTable(
        name = "article_content",
        pkJoinColumns = @PrimaryKeyJoinColumn(name = "id")
    )
    public class Article{
        ...
    
        @AttributeOverrides({
            @AttribueOverride(name = "content", column = @Column(table = "article_content")),
            @AttribueOverride(name = "contentType", column = @Column(table = "article_content"))
        })
        private ArticleContent articleContent;
    }
    ```
    
## Chapter 05 - 리포지터리와 조회 기능
생략

## Chapter 06 - 응용 서비스와 표현 영역

* 표현영역과 응용영역
  * 표현영역은 사용자의 요청을 해석
  * 응용영역은 사용자가 실제 원하는 기능을 제공
  * 표현영역은 응용 서비스가 요구하는 형식으로 사용자 요청을 변환
  * 응용영역은 표현영역에 의존하지 않는다.
  
* 응용 서비스 구현
  * 응용 서비스 클래스 내에서 2~3개의 기능을 구현하자(범균님 추천)
  * 별도 클래스에 로직을 구현해서 코드 중복을 방지
    ```java
    public final class MemberServiceHelper {
        public static Member findExistinggMember(MemberRepository repo, String id){
            ...
        }
    }

    import static com.myshop.member.application.MemberServiceHelper.*;
    ```
* 응용 서비스는 표현영역에 필요한 데이터만 리턴
  * 로직의 응집도를 높이는 확실한 방법
* 표현영역에 의존하지 않기
  * 표현 영역과 관련된 타입 사용하지 말자.
    * ex) HttpServletRequest, HttpSession ....
    * 응용 서비스만 단독으로 테스트 하기 어려움.
    * 표현 영역이 변경되면 응용 서비스 영역도 변경되어야 한다.
* 표현영역
  * 사용자의 세션을 관리한다.
* 값 검증
  * 표현영역 : 필수 값, 값의 형식, 범위 등 검증
  * 응용 서비스 : 데이터의 존재 유무와 같은 논리적 오류 검
  
## Chapter 07 - 도메인 서비스

* 여러 애그리거트가 필요한 기능
  * 한 애그리거트에 억지로 구현하지 말고 도메인 서비스를 별도로 구현해라
* 도메인 서비스
  * 응용 영역의 서비스 = 응용로직, 도메인 서비스 = 도메인 로직
  * 도메인 개념을 명시적으로 드러낼 것. 도메인의 의미가 드러나는 용어를 타입과 메서드로 명명
    ```java
    public class DiscountCalculationService {
        public Money calculateDiscountAmounts(
            List<OrderLine> orderLines,
            List<Coupon> coupons,
            MemberGrade grade){
            
            ...
        }
    }
    ```
  * 도메인 서비스 사용주체는 애그리거트일 수도, 응용서비스 일수도 있다.
  * 다음과 같이 애그리거트에 도메인 서비스 전달. 전닳하는 것은 응용 서비스의 책임 
    ```java
    public class Order{
        public void calculateAmounts(
                DiscountCalculateService discountCalculateService, MemberGrade grade){
            Money discountAmounts = 
                discountCalculateService.calculateDiscountAmounts(this.orderLines, this.coupons, grade);
            ...
        }
    }
    ```
  * 패키지 위치
    * 도메인 영역에 위치