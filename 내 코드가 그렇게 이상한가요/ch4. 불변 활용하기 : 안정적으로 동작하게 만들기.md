### 4. 불변 활용하기 : 안정적으로 동작하게 만들기

- 재할당
    - 변수에 값을 다시 할당하는 것을 재할당이라하며 재할당은 변수의 의미를 바꿔 추측하기 어렵고 언제 어떻게 변경되었는지 추척하기가 어렵다.
- 재할당을 방지하기 위해 불변 변수로 만들기
    - 지역변수는 물론 메소드의 매개변수도 불변 변수로 변경한다.
- 가변으로 인해 발생하는 의도하지 않은 영향
    
    ```java
    class OrderItem {
        private String productName;
        private double price;
        private int quantity;
    }
    
    class Order {
        final OrderItem orderItem;
    
    		Order(OrderItem orderItem) {
            this.orderItem = orderItem;
        }
    }
    
    public class OrderServiceDemo {
        public static void main(String[] args) {
            OrderItem sharedOrderItem = new OrderItem("Laptop", 1000, 1);
    
            Order order1 = new Order(sharedOrderItem);
    				Order order2 = new Order(sharedOrderItem);
    
            // order1의 가격 변경
            order1.orderItem.price(1200);
    
            System.out.println("Order1 Total Price after: " + order1.OrderItem.price);
            System.out.println("Order2 Total Price after: " + order2.OrderItem.price);
        }
    }
    ```
    
    - 지금 보면 order1의 가격만 변경을 하였는데, 출력을 하게 되면 order1, order2 두 객체 모두 가격이 1200으로 변경되었다.
    - 이처럼 가변 인스턴스 변수는 예상하지 못한 동작을 일으킨다.
- 부수 효과의 단점
    - 함수의 부수 효과는 '함수가 매개변수를 전달받고, 값을 리턴하는 것' 이외에 외부 상태(인스턴스 변수 등)을 변경하는 것을 가르킨다.
    - 상태 변경이란 함수 밖에 있는 상태를 변경하는 것을 의미한다.
        - 인스턴스 변수 변경
        - 전역 변수 변경
        - 매개변수 변경
        - 파일 읽고 쓰기 같은 I/O 조작
- 함수의 영향 범위 한정하기
    - 함수는 아래 항목을 만족하도록 설계하는 것이 좋다.
        - 상태는 매개변수로 받는다.
        - 상태를 변경하지 않는다.
        - 같은 함수의 리턴 값으로 돌려준다.
    - 잘못된 예시
        
        ```java
        public class OrderItem {
            private double price;
            private int quantity;
            private static double discount = 0.1;  // 전역적인 할인율
        
            public void calculateDiscountedPrice() {  // 상태를 매개변수로 안 받는다.
                this.price * (1 - discount);  // 상태를 변경한다.
            }
        }
        ```
        
    - 올바른 예시
        
        ```java
        public class OrderItem {
            private final double price;  // 불변성을 위해 final 키워드 사용한다.
            private final int quantity;  // 불변성을 위해 final 키워드 사용한다.
        
            public OrderItem(double price, int quantity) {
                this.price = price;
                this.quantity = quantity;
            }
        
            public double calculateDiscountedPrice(double discount) {  // 상태는 매개변수로 받는다.
                return price * (1 - discount);  // 상태를 변경하지 않고, 같은 입력에 대해 항상 같은 값을 반환한다.
            }
        }
        ```
        
        - calculateDiscountedPrice에 새로운 객체를 반환하지 않았던 이유는 기존 객체의 상태를 변경하거나 새로운 OrderItem 객체를 생성하는 것이 아니라, 단순히 계산된 값만 반환하기 위함이다.
        - 메소드의 목적에 따라 다를수 있지만 해당 메소드는 객체의 상태를 변경하는 것이 아니라 특정 값만 계산하여 반환하는거라 새로운 객체를 반환하여 불변성을 유지시킬 이유가 없다. 라고 생각했다.
        - 만약 상품의 개수(quantity)를 변경하는 거라면 새로운 객체를 생성해서 불변상태를 유지 시켜주는 것이 좋을 것이다.
            
            ```java
            public OrderItem updateQuantity(final int newQuantity) {
                return new OrderItem(this.price, newQuantity);
            }
            ```
            
- 기본적으로는 불변으로
    - 장점
        - 변수의 의미가 변하지 않으므로, 혼란을 줄일 수 있음
        - 동작이 안정적이게 되므로, 결과를 예측하기 쉬움
        - 코드의 영향 범위가 한정적이므로, 유지 보수가 편리해짐
- 가변으로 설계해야 하는 경우
    - 대량의 데이터를 빠르게 처리해야하는 경우
    - 이미지를 처리하는 경우
    - 위와 같이 가변으로 설계해야하는 이유는?
        - 불변이라면 값을 변경할 때 인스턴스를 새로 생성해야한다.
        - 만약 크기가 큰 인스턴스를 새로 생성하면서 시간이 오래 걸려 성능에 문제가 있다면, 불변보단 가변을 사용해야한다.
- 상태를 변경하는 메서드 설계하기
    - 상태를 변화시키는 메소드를 뮤테이터(Mutater)라고 부른다.
    - 뮤테이터의 예시는 아래와 같다.
        
        ```java
        public class OrderItem {
            private final double price;
            private int quantity;
        
            // 수량 변경 메서드 (가변성 도입)
            public void updateQuantity(int newQuantity) {
                this.quantity = newQuantity;
            }
        }
        ```
        
        - 웹 개발자라면 한번쯤 이렇게 코드를 짜보지 않을까 한다.
        - 해당 도메인 엔티디에 대해 값 변경을 메소드를 통해 이뤄지고, Repo를 통해 저장을 할 것이다.
        - 이런 경우도 있다라는 예시로 들어보았다.