### 3. 클래스 설계 : 모든 것과 연결되는 설계 기반

- 잘 만들어진 클래스는 두가지로 구성 되어야한다.
    1. 인스턴스 변수
        - 객체가 생성될 때마다 독립적인 메모리 공간을 갖게 되는 변수이다.
        - Java에서는 접근 제한자로 `Private`, `Public`, `Protected`로 사용되며 일반적으로 캡슐화 원칙에 따라 `private`로 설정되어 외부로부터 직접 접근이 제한된다.
    2. 인스턴스 변수에 잘못된 값이 할당되지 않게 막고, 정상적으로 조작하는 메소드
        - 이는 캡슐화 원칙의 일환이다. 캡슐화는 객체의 상태를 외부에서 직접 변경하지 못하게 하고, 대신 정해진 메서드를 통해서만 상태를 변경하도록 제한하는 원칙이다.
        - 예를 들어, 인스턴스 변수가 `private`로 선언되었다면, 해당 변수를 직접 조작할 수 없다. 대신, 이 변수의 값을 조회하거나 변경하기 메서드를 사용한다.
- 모든 클래스가 갖추어야 하는 자기 방어 임무
    - 다른 클래스를 사용해서 초기화와 유효성 검사를 해야 하는 클래스는 그 자체로는 안전하게 사용할 수 없는 미성숙한 클래스이다.
    - 클래스는 스스로 자기 방어 임무(혼자서 초기화와 유효성 검사 가능해야 함)를 수행할 수 있어야 소프트웨어의 품질을 높이는 데 도움이 된다.
- 성숙한 클래스로 성장시키는 설계 기법
    - 잘못된 값을 들어간 인스턴스가 생성되지 못하게 생성자를 통해 유효성을 검사한다.
        
        ```java
        class OrderItem {
            private String productName;
            private double price;
            private int quantity;
        
            public OrderItem(String productName, double price, int quantity) {
                if (price < 0) {
                    throw new IllegalArgumentException("Price cannot be negative");
                }
                if (quantity < 0) {
                    throw new IllegalArgumentException("Quantity cannot be negative");
                }
        
                this.productName = productName;
                this.price = price;
                this.quantity = quantity;
            }
        }
        ```
        
    - 불변 변수로 만들어 예상하지 못한 동작 막기
        
        ```java
        class OrderItem {
            private final String productName;
            private final double price;
            private final int quantity;
        
            public double calculateTotalPriceForItem() {
                return price * quantity;
            }
        }
        ```
        
        - 이렇게 되면 생성자를 통해서만 생성이 가능하다.
    - 값을 변경하고 싶다면 새로운 인스턴스 만들기
        
        ```java
        class OrderItem {
            private final String productName;
            private final double price;
            private final int quantity;
        
            public double calculateTotalPriceForItem() {
                return price * quantity;
            }
        
            public OrderItem updatedQuantity(int newQuantity) {
                return new OrderItem(productName, price, newQuantity);
            }
        }
        ```
        
        - 이렇게 하면 불변을 유지하면서도 값을 변경할 수 있다.
- 위와 마찬가지로 메소드의 매개변수, 지역 변수도 불변으로 만들수 있다.
- 값 객체
    - 값 객체란 값을 클래스(자료형)로 나타내는 디자인 패턴이다.
    - 금액, 날짜 등을 사용하게 되면 각각의 값과 로직이 있기에 응집도가 높은 구조로 만들 수 있다.
    - 예를 들어 금액을 단순한 int형으로 두었다면 상품의 개수 가격등에 사용이 되며 실수로 의미가 다른 값이 섞을수도 있다.
    - 값 객체 + 완전 생성자는 객체 지향 설계에서 많이 사용되는 기법이다.