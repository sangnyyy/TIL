# Object
내맘대로 정리한 Object

## Chapter 01 - 객체, 설계
* 모든 소프트웨어 모듈의 3가지 원칙 (by 로버트 마틴)
  1) 제대로 동작하는 것 (이건 왠만하면 만족할 것)
  2) 변경에 용이할 것
  3) 코드를 읽는 사람이 쉽게 이해(의사소통)할 것(= 예상에서 크게 벗어나지 않을 것)

* 변경에 취약한 코드
  * 의존성이 많다. 최소한의 의존성만을 가지자.
  * 결합도가 높다. 응집도가 낮다.

    ```java
    public class Theater {
        private TicketSeller ticketSeller;

        public Theater(TicketSeller ticketSeller) {
            this.ticketSeller = ticketSeller;
        }

        public void enter(Audience audience) {
            if (audience.getBag().hasInvitation()) {
                Ticket ticket = ticketSeller.getTicketOffice().getTicket();
                audience.getBag().setTicket(ticket);
            } else {
                Ticket ticket = ticketSeller.getTicketOffice().getTicket();
                audience.getBag().minusAmount(ticket.getFee());
                ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
                audience.getBag().setTicket(ticket);
            }
        }
    }
    ```

* 더 나은 설계?
  * 객체를 자율적인 존재로 만들자
    ```java
    ticketSeller.getTicketOffice().getTicket();  //bad
    ticketSeller.sellTo(audience);  //better
    ```

* 캡슐화
  * 객체 내부의 세부적인 사항을 감추는 것
  * 내부로의 접근을 막고 결합도를 낮춘다.

* 인터페이스와 구현의 분리
  * 결합도를 낮추고, 변경하기 쉬운 코드 작성을 위한 기본 설계 원칙

* Data와 Process를 동일한 모듈 내부로 하나로 모으는 것은 객체지향의 기본

* 어떤 기능을 설계하는 방법은 한가지 이상일 수 있다.
  * 꼭 객체지향적인 구현이 정답은 아닐 수 있다.
  * 적절한 의논을 통해 문제를 해결하자.
    ```java
    public class TicketOffice{
        // 객체 내부에서 구현을 하여 응집도를 높였으나, 
        // audience에 대한 의존성(어찌보면 불필요한?)이 생겼다. 
        // 의논이 필요한 시점!
        public void sellTicketTo(Audience audience) {
            plusAmount(audience.buy(getTicket()));
        }
    }
    ```
* 협력하는 객체들간의 의존성을 적절히 조절하자
  * 다른 객체로의 "메시지 전달"을 위해서는 의존성을 가질 수 밖에 없다.
  * 의존성이 무조건적으로 없어져야 하는 것은 아니다.