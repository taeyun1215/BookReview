### 5. **응집도 : 흩어져 있는 것들**

- static 메소드 오용
    - static 메소드는 전역적으로 사용이 가능하다. → 클래스 인스턴스를 생성하지 않는다.
    - static 메소드의 구조적 문제
        - 데이터와 비즈니스 로직이 분리되어 있어서 응집도가 낮다.
    - static 메소드를 사용한 예시
        
        ```java
        public class Order {
            private List<OrderItem> orderItems;
        
            // 응집도가 낮은 static 메소드
            public static double calculateVAT(double price) {
                return price * 0.1;  // 부가세 10% 계산
            }
        }
        
        // 인스턴스를 생성하지 않고 호출
        double vatAmount = Order.calculateVAT(totalPrice);
        ```
        
- 인스턴스 메서드인 척 하는 static 메소드 주의하기
    
    ```java
    public class Order {
        private List<OrderItem> orderItems;
    
    		// 인스턴스 메소드인 척 하는 static 메소드
        public double calculateDiscountedPrice(double originalPrice, double discountRate) {
            return originalPrice * (1 - discountRate);
        }
    }
    ```
    
    - `calculateDiscountedPrice` → orderItems을 전혀 사용하지 않는 메소드이다.
    - 이렇게 되면 앞에서 예시로 보여줬던 `calculateVAT`와 거의 동일하다.
- static 메소드를 왜 사용할까?
    - static 메소드는 절차 지향 언어의 접근 방법에서 사용한다.
    - 절차 지향 언어에서는 데이터와 비즈니스 로직이 별개로 존재하도록 설계한다.
    - 따라서 클래스의 인스턴스를 생성하지 않고도 사용할 수 있는 static 메소드를 사용한다.
    - static 메소드는 클래스의 인승턴스를 만들지 않아도 되기에 간단하게 사용이 가능하지만, 응집도가 낮아지는 문제가 있다. 적재적소에 사용하는 것이 중요하다.
- 어떤 상황에서 static 메소드를 사용해야 좋을까?
    - 로그 출력 전용 메소드, 포맷 변환 전용 메소드 등에서 쓰인다.
    - 해당 메소드들은 응집도와 관계가 없기에 static으로 사용하는게 좋다.
- 매개변수가 너무 많은 경우
    - 매개변수가 많아지면 응집도가 낮아질 수 있다.
    - 그 이유는 아래와 같다.
        - 해당 메소드에서 너무 많은 일을 하려고 할 수 있기에 이는 저 응집도의 신호이다. 즉, 하나의 메소드가 여러 가지 책임을 지니고 있을 가능성이 높다.
- 기본 자료형에 대한 집착
    - 기본 자료형에 대한 집착을 하게 되는 경우 데이터와 비즈니스 로직이 분산된다.
    - 그렇게 되면 중복된 코드가 많이 생기므로 응집도가 낮아진다.
    - 잘못된 예시 → 기본 자료형만 사용한 코드
        
        ```java
        public class OrderService {
            
            public double applyDiscount(double orderAmount, double discountRate) {
                if (orderAmount < 0) {
                    throw new IllegalArgumentException("Amount cannot be negative");
                }
                if (discountRate < 0) {
                    throw new IllegalArgumentException("discountRate cannot be negative");
                }
                return orderAmount * (1 - discountRate);
            }
        
            public double calculateVAT(double orderAmount) {
                if (orderAmount < 0) {
                    throw new IllegalArgumentException("Amount cannot be negative");
                }
                return orderAmount * 0.1;  // 10% VAT
            }
        }
        ```
        
        - 매개변수 orderAmount의 유효성 검사를 중복되게 하는 것을 볼 수 있다.
    - 올바른 예시 → 기본 자료형을 클래스로 응집한 코드
        
        ```java
        
        public class OrderAmount {
            private final double amount;
        
            public OrderAmount(double amount) {
                if (amount < 0) {
                    throw new IllegalArgumentException("Amount cannot be negative");
                }
                this.amount = amount;
            }
        }
        
        // DiscountRate 클래스
        public class DiscountRate {
            private final double rate;
        
            public DiscountRate(double rate) {
                if (rate < 0 || rate > 1) { // 0 ~ 1 사이의 값만 유효하다고 가정
                    throw new IllegalArgumentException("Discount rate must be between 0 and 1");
                }
                this.rate = rate;
            }
        
            public double applyTo(double amount) {
                return amount * (1 - rate);
            }
        }
        
        // OrderService 클래스
        public class OrderService {
            private final OrderAmount orderAmount;
            private final DiscountRate discountRate;
        
            public OrderService(OrderAmount orderAmount, DiscountRate discountRate) {
                this.orderAmount = orderAmount;
                this.discountRate = discountRate;
            }
        
            public double applyDiscount() {
                return discountRate.applyTo(orderAmount.getAmount());
            }
        
            public double calculateVAT() {
                return orderAmount.getAmount() * 0.1;
            }
        }
        ```
        
        - 이렇게 만들어진 코드는 다양한 장점이 있다.
            1. 캡슐화 : OrderAmount, DiscountRate 클래스는 자신의 상태와 관련된 유효성 검사와 동작을 자체적으로 처리한다. 이는 외부에서 이 클래스들의 내부 상태나 구현 방식을 알 필요 없이 사용할 수 있다는 것을 의미한다.
            2. 응집도 : OrderAmount는 주문 금액과 관련된 로직만, DiscountRate는 할인율과 관련된 로직만 책임지게 된다. 각 클래스는 그에 해당하는 역할만을 수행하므로 응집도가 높아진다.
            3. 재사용성 : OrderAmount나 DiscountRate와 같은 클래스는 다른 서비스나 기능에서도 사용될 가능성이 있다. 이렇게 독립적으로 잘 정의된 클래스는 다른 부분에서도 재사용하기 쉽다.
- 묻지 말고 명령하기
    - 다른 객체의 내부 상태(변수)를 기반으로 판단하거나 제어하려고 하지 말고, 메소드로 명령해서 객체가 알아서 판단하고 제어하도록 설계하라는 의미이다.
    - 잘못된 예시
        
        ```java
        public class Order {
            private ShippingStatus shippingStatus;
        
            public ShippingStatus getShippingStatus() {
                return shippingStatus;
            }
        
            public void setShippingStatus(ShippingStatus status) {
                this.shippingStatus = status;
            }
        }
        
        public class OrderService {
            public void shipOrder(Order order) {
        				// order 객체에게 묻고 있다.
                if (order.getShippingStatus() == ShippingStatus.PENDING) {
                    order.setShippingStatus(ShippingStatus.SHIPPED);
                }
            }
        }
        ```
        
    - 올바른 예
        
        ```java
        public class Order {
            private ShippingStatus shippingStatus;
        
            public void ship() {
                if (this.shippingStatus == ShippingStatus.PENDING) {
                    this.shippingStatus = ShippingStatus.SHIPPED;
                }
            }
        }
        
        public class OrderService {
            public void shipOrder(Order order) {
        				// order 객체에게 묻는 것이 아닌 order 객체가 주체적으로 제어한다.
                order.ship();
            }
        }
        ```