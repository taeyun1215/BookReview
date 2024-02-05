### 8. 강한 결합 : 복잡하게 얽혀서 풀 수 없는 구조

- 결합이 생긴 여러 코드
    - 결합도가 높으면 안 좋은 점
        - 한 클래스의 변경이 다른 의존하는 클래스 모두 영향을 미친다.
        - 의존 받는 클래스가 변경되면 의존 하는 클래스들은 변경이 필수적이다. → 유지보수가 어려움.
        - 단일 책임 원칙을 위반한다.
    - 안 좋은 예시
        
        ```java
        public class OrderService {
            private List<Order> orders = new ArrayList<>();
            private int totalAmount = 0;
        
            public boolean addOrder(Order order) {
                if (!isValid(order)) {
                    throw new IllegalArgumentException();
                }
        
                int amountAfterDiscount = order.getPrice();
                if (order.isDiscountApplicable()) {
                    amountAfterDiscount = applyDiscount(order.getPrice());
                }
        
                if (totalAmount + amountAfterDiscount <= 10000) {
                    totalAmount += amountAfterDiscount;
                    orders.add(order);
                    return true;
                } else {
                    return false;
                }
            }
        
            private boolean isValid(Order order) {
                return order.getId() >= 0 && !order.getName().isEmpty();
            }
        
            private int applyDiscount(int price) {
                return price - 100; 
            }
        }
        
        public class SeasonalOrderService {
            private OrderService orderService = new OrderService();
        
            public boolean addSeasonalOrder(Order order) {
                if (order.getId() < 0 || order.getName().isEmpty()) {
                    throw new IllegalArgumentException();
                }
        
                int amountAfterDiscount = order.getPrice();
                if (order.isDiscountApplicable()) {
                    amountAfterDiscount = orderService.applyDiscount(order.getPrice());  // Problem here
                }
        
                if (orderService.totalAmount + amountAfterDiscount <= 12000) {  // Problem here
                    orderService.totalAmount += amountAfterDiscount;  // Problem here
                    orderService.orders.add(order);  // Problem here
                    return true;
                } else {
                    return false;
                }
            }
        }
        ```
        
        - **`SeasonalOrderService`**가 **`OrderService`**의 내부 로직과 상태를 변경하고 있습니다.
        - 이는 단일 책임 원칙을 위배하며, 두 클래스가 강하게 결합됩니다.
- 중복된 코드를 핸들링하기
    - 중복된 코드를 핸들링 하기 위해서는 아래와 같은 방법들이 있다.
        - 상속
        - 컴포지션
        - 전략패턴
    - 상속은 후에 다룰 예정이라 넘어가겠다.
    - 컴포지션과 전략패턴이 유사한 내용을 띄고 있지만 내가 생각하기엔 약간에 차이가 있는 것 같다.
    - 둘의 차이는 동일한 코드의 중복을 피하려면 컴포지션을 사용하고 Class를 이용한다. 동일한 문제를 해결하는 다양한 방법(전략)을 유연하게 교체하려면 전략 패턴을 사용하고 Interface를 사용한다.
    - 컴포지션 + 전략패턴 예시
        
        ```java
        // 전략 패턴(기본 할인 정책과 여름 할인 정책이 개념(할인 정책)은 같으나 비즈니스 로직이 다름.)
        interface DiscountPolicy {
            int MIN_AMOUNT = 0;  // 최소 할인 금액
            int applyDiscount(int price);
        }
        
        // 할인 정책 1 (기본 할인 정책)
        class FlatDiscountPolicy implements DiscountPolicy {
            private final int discountAmount;
        
            FlatDiscountPolicy(int discountAmount) {
                this.discountAmount = discountAmount;
            }
        
            @Override
            public int applyDiscount(int price) {
                return Math.max(price - discountAmount, MIN_AMOUNT);
            }
        }
        
        // 할인 정책 2 (여름 할인 정책)
        class PercentageDiscountPolicy implements DiscountPolicy {
            private final double discountPercentage;
        
            PercentageDiscountPolicy(double discountPercentage) {
                this.discountPercentage = discountPercentage;
            }
        
            @Override
            public int applyDiscount(int price) {
                return (int) Math.max(price * (1 - discountPercentage), MIN_AMOUNT);
            }
        }
        
        // OrderBasicSaleService , OrderSummerSaleService 동일 코드 분리
        class OrderProcessor {
            private final DiscountPolicy discountPolicy;
        
            OrderProcessor(DiscountPolicy discountPolicy) {
                this.discountPolicy = discountPolicy;
            }
        
            public int processOrder(Order order) {
                int amountAfterDiscount = order.getPrice();
                if (order.isDiscountApplicable()) {
                    amountAfterDiscount = discountPolicy.applyDiscount(order.getPrice());
                }
                return amountAfterDiscount;
            }
        }
        
        // 기본 할인
        class OrderBasicSaleService {
            private final OrderProcessor orderProcessor; // 컴포지션(has-a 관계)
        
            OrderBasicSaleService() {
                this.orderProcessor = new OrderProcessor(new FlatDiscountPolicy(100));
            }
        
            public boolean addOrder(Order order) {
                int finalAmount = orderProcessor.processOrder(order); // 위임(orderProcessor에게 위임함.)
                return true;
            }
        }
        
        // 여름 할인
        class OrderSummerSaleService {
            private final OrderProcessor orderProcessor; // 컴포지션(has-a 관계)
        
            OrderSummerSaleService() {
                this.orderProcessor = new OrderProcessor(new PercentageDiscountPolicy(0.1));
            }
        
            public boolean addOrder(Order order) {
                int finalAmount = orderProcessor.processOrder(order); // 위임(orderProcessor에게 위임함.)
                return true;
            }
        }
        ```
        
        - 이렇게 만들게 되면 재사용성에 유용하다. → 새로운 할인 정책이 나온다고 했을 때 `OrderProcessor`를 변경하는 것이 아닌 새로운 클래스를 만들고 생성자를 통해 생성하면 된다.
        - 단일 책임 원칙을 지킨다. → `OrderProcessor`는 할인 로직을 처리하는 책임만 가지고 있으므로 서비스 클래스들은 주문 처리 로직에만 집중하면 된다.
- DRY(Don’t Repaet Yourself) 원칙의 잘못된 적용
    - 위에서 예시로 만든 `OrderService`, `SeasonalOrderService` 두 클래스는 거의 유사한 코드를 가지고 있다.
    - 유사한 코드를 무작정 줄이고 싶은 생각이 들기도 하지만, 책무를 생각하지 않고 로직의 중복을 제거해서는 안 된다.
    - 예를 들어 `OrderService`, `SeasonalOrderService`를 컴포지션를 사용해서 묶었다고 가정 했을 때, 갑자기 `SeasonalOrderService`의 할인 정책이 바뀌게 된다면 오히려 중복을 제거하려 했다가 더 큰 공사를 하게 될 것이다.
    - **따라서 같은 로직, 비슷한 로직이라도 개념이 다르면 중복을 허용해줘야한다.**
- 상속과 관련된 강한 결합
    - 상속을 사용하게 되면 단점
        - 하위 클래스가 상위 클래스의 구현에 의존하게 되어, 상위 클래스의 변경이 하위 클래스에 영향을 줄 수 있다. → 강한 결합이 생김.
        - 하위 클래스가 상위 클래스의 내부 구조와 상태에 접근이 가능하다. → 캡슐화 유지 어려움.
    - 해결하기 위해서는 컴포지션, 전략 패턴, 위임으로 해결이 가능하다.
- Private 메소드가 많다는 것은 책임이 너무 많다는 것
    - private 메소드를 많이 사용하는 것보단 책임을 다른 클래스로 분리하는 것이 낫다.