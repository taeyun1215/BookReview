### 6. 조건 분기 **: 미궁처럼 복잡한 분기 처리를 무너뜨리는 방법**

- 조건 분기가 중첩되어 낮아지는 가독성
    - 예시
        
        ```java
        if (조건) {
        		// 수십 ~ 수백 줄의 코드
        		if (조건) {
        				// 수십 ~ 수백 줄의 코드
        				if (조건) {
        						// 수십 ~ 수백 줄의 코드
        						if (조건) {
        								// 수십 ~ 수백 줄의 코드
        						}
        				}
        		}
        }
        ```
        
        - 이처럼 코드를 짠 사람도 이해하기 어려울 정도의 중첩 분기문인데, 팀 단위로 같이 작업할 땐 얼마나 더 심하겠는가
- 조기 리턴으로 중첩 제거하기
    - 예시
        
        ```java
        public class OrderService {
        
            public String processOrder(Order order) {
                if (!order.isPaid()) return "Order has not been paid!";
                if (!order.isInStock()) return "Item is out of stock!";
                if (!order.hasValidShippingAddress()) return "Invalid shipping address!";
                
                order.ship();
        
                return "Order has been shipped!";
            }
        }
        ```
        
        - 조기 리턴으로 중첩이 제거 되어서 가독성이 좋아졌다.
    - 요구사항이 추가되었다고 가정하게 된다면, 아래와 같다.
        
        ```java
        public class OrderService {
        
            public String processOrder(Order order) {
                if (!order.isPaid()) return "Order has not been paid!";
                if (!order.isInStock()) return "Item is out of stock!";
                if (!order.hasValidShippingAddress()) return "Invalid shipping address!";
        				if (order.getItemCount() < 2) order.addShippingFee();  // 분기 요구사항
        
                order.ship();
        				order.setFreeShipping();  // 비즈니스 요구사항
        
                return "Order has been shipped!";
            }
        }
        ```
        
        - 위와 같이 매우 간단하게 요구사항을 추가할 수 있다.
- 전략 패턴(정책 패턴)
    - 전략패턴이란 여러 유사한 알고리즘을 캡슐화해서 객체의 행위를 동적으로 변경 가능하게 만드는 패턴이다.
    - OCP(개방폐쇄의 원칙) : 변경엔 닫혀있고 확장엔 열려있는 객체지향 원칙이 실현된다.
        - 다른 행동 및 전략이 추가된다고 해도 기존의 코드는 변경하지 않고 확장이 가능하다.
    - 전략 패턴을 사용하지 않은 예
        
        ```java
        public class DiscountService {
            public double applyDiscount(double orderAmount, String discountType) {
                if ("PERCENT_10".equals(discountType)) {
                    return orderAmount * 0.9;
                } else if ("PERCENT_20".equals(discountType)) {
                    return orderAmount * 0.8;
                } else if ("FLAT_50".equals(discountType)) {
                    return orderAmount - 50;
                }
                return orderAmount;
            }
        }
        ```
        
        - 이 방식의 문제점은 새로운 할인 유형이 추가될 때마다 applyDiscount 메서드를 수정해야 한다는 것이다.
        - 또한, DiscountService 클래스에만 이런 조건이 있는게 아니라 다른 클래스에도 유사한 조건이 있다면 모두 수정이 필요하다.
    - 전략 패턴을 사용 예
        
        ```java
        interface DiscountStrategy {
            double apply(double orderAmount);
        }
        
        class Percent10Discount implements DiscountStrategy {
            public double apply(final double orderAmount) {
                return orderAmount * 0.9;
            }
        }
        
        class Percent20Discount implements DiscountStrategy {
            public double apply(final double orderAmount) {
                return orderAmount * 0.8;
            }
        }
        
        class Flat50Discount implements DiscountStrategy {
            public double apply(final double orderAmount) {
                return orderAmount - 50;
            }
        }
        
        public class DiscountService {
            private DiscountStrategy strategy;
        
            public DiscountService(DiscountStrategy strategy) {
                this.strategy = strategy;
            }
        
            public double applyDiscount(double orderAmount) {
                return strategy.apply(orderAmount);
            }
        }
        ```
        
        - 이제 새로운 할인 유형이 추가되면 DiscountService 클래스를 수정할 필요 없이 새로운 DiscountStrategy 구현만 추가하면 된다.
        - 이는 코드의 확장성을 향상시키고, 변경에 대한 영향을 최소화하는 데 도움을 줍니다.
- 자료형 확인에 조건 분기 사용하지 않기
    - 자료형을 조건 분기로 사용하게 되면 모처럼 if문을 없애기 위해 전략 패턴을 사용했는데 다시금 생기는 것입니다.
    - 처음에 한번 생기는 건 괜찮아 질수도 있지만, 지름길을 찾게 된다면 점점 유지보수 하기 어려운 코드를 만들게 될 것입니다. 이러한 방식을 깨진 유리창 이론이라고 합니다.
    
    <aside>
    💡 깨진 유리창 이론이란?
    
    - 품질이 떨어진 코드에서 작업할 때 더 낮은 품질의 코드를 추가하기가 쉽다.
    - 코딩 규칙을 많이 어긴 코드에서 작업할 때 또 다른 규칙을 어기기도 쉽다.
    - 지름길을 많이 사용한 코드에서 작업할 때 또 다른 지름길을 추가하기도 쉽다.
    - 즉, 레거시 코드가 될 가능성이 높습니다.
    </aside>
    
    - 자료형 확인에 조건 분기를 사용한 안 좋은 예시
        
        ```java
        public class DiscountService {
            private DiscountStrategy strategy;
        
            public DiscountService(DiscountStrategy strategy) {
                this.strategy = strategy;
            }
        
            public double applyDiscount(double orderAmount) {
                if (strategy instanceof Flat50Discount) {
                    orderAmount += 30;
                }
                return strategy.apply(orderAmount);
            }
        }
        ```
        
        - 이렇게 되면 점점 분기문이 추가될 것이다.
    - 자료형 확인에 조건 분기를 사용하지 않은 좋은 예시
        
        ```java
        interface DiscountStrategy {
            double apply(double orderAmount);
        }
        
        class Flat50Discount implements DiscountStrategy {
            public double apply(final double orderAmount) {
                return orderAmount - 50 + 30;
            }
        }
        
        public class DiscountService {
            private DiscountStrategy strategy;
        
            public DiscountService(DiscountStrategy strategy) {
                this.strategy = strategy;
            }
        
            public double applyDiscount(double orderAmount) {
                return strategy.apply(orderAmount);
            }
        }
        ```
        
        - 이렇게 하면 분기문을 추가하지 않고 처리가 가능하다.
    - 단, DiscountStrategy의 구현체인 다른 클래스도 공통된 로직이 추가가 된다면 DiscountStrategy에 새로운 메소드를 구현해서 모든 구현체에서도 해당 비즈니스 로직을 추가해주면 될 것이다.