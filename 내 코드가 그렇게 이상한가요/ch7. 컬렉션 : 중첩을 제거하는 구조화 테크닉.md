### 7. 컬렉션 : 중첩을 제거하는 구조화 테크닉

- 이미 존재하는 기능을 다시 구현하지 말자
    - for문과 if문이 동시에 있는 코드는 가독성이 좋지 못합니다.
    - 안 좋은 예시
        
        ```java
        public boolean hasAnyProcessingOrder(List<Order> orders) {
            for (Order order : orders) {
                if ("processing".equals(order.getStatus())) {
                    return true;
                }
            }
            return false;
        }
        ```
        
    - 좋은 예시
        
        ```java
        public boolean hasAnyProcessingOrder(List<Order> orders) {
            return orders.stream().anyMatch(order -> "processing".equals(order.getStatus()));
        }
        ```
        
        - anyMatch 메소드를 알고 있으면, 복잡한 로직을 직접 구현하는 것이 아닌 한줄로 처리가 가능하다.
- 조기 Contiune로 조건 분기 중첩 제거하기
    - for문 안에 조건 분기문이 중첩 되어 있는 경우 가독성이 좋지 못합니다.
    - 안 좋은 예시
        
        ```java
        public boolean hasSpecificOrder(List<Order> orders) {
            for (Order order : orders) {
                if ("processing".equals(order.getStatus())) {
                    if (order.getAmount() > 1000) {
                        if (order.getOrderDate().isEqual(LocalDate.now())) {
                            order.setStatus("processed");
        				            System.out.println("Order has been processed: " + order);
                        }
                    }
                }
            }
        }
        ```
        
    - 좋은 예시
        
        ```java
        public boolean hasSpecificOrder(List<Order> orders) {
            for (Order order : orders) {
                if (!"processing".equals(order.getStatus())) continue;
                if (order.getAmount() <= 1000) continue;
                if (!order.getOrderDate().isEqual(LocalDate.now())) continue;
        
                order.setStatus("processed");
                System.out.println("Order has been processed: " + order);
            }
        }
        ```
        
        - 중첩 분기문으로 들여쓰기 하는 것보다 훨씬 더 나은 코드의 형태를 볼 수 있다.
- 응집도가 낮은 컬렉션 처리하기
    - 컬렉션과 관련된 작업을 처리하는 코드는 여기저기 생길 수가 있다.
    - 안 좋은 예시
        
        ```java
        public class OrderService {
            private List<Order> orders;
        
            public boolean isEligibleOrders() {
                return orders.stream().anyMatch(order -> 
                    "processing".equals(order.getStatus()) &&
                    order.getAmount() > 1000 &&
                    order.getOrderDate().isEqual(LocalDate.now())
                );
            }
        }
        
        public class ReportService {
            private List<Order> orders;
        
            public List<Order> findEligibleOrders() {
                return orders.stream().filter(order -> 
                    "processing".equals(order.getStatus()) &&
                    order.getAmount() > 1000 &&
                    order.getOrderDate().isEqual(LocalDate.now())
                ).collect(Collectors.toList());
            }
        }
        ```
        
    - 좋은 예시
        
        ```java
        public class Orders {
            private List<Order> orders;
        
            public Orders(List<Order> orders) {
                this.orders = orders;
            }
        
            public boolean isEligibleOrders() {
                return orders.stream().anyMatch(this::matchesCriteria);
            }
        
            public List<Order> findEligibleOrders() {
                return orders.stream().filter(this::matchesCriteria).collect(Collectors.toList());
            }
        
            private boolean matchesCriteria(Order order) {
                return "processing".equals(order.getStatus()) &&
                       order.getAmount() > 1000 &&
                       order.getOrderDate().isEqual(LocalDate.now());
            }
        }
        ```
        
        ```java
        public class OrderService {
            private Orders orders;
        
            public boolean isEligibleOrders() {
                return orders.isEligibleOrders();
            }
        }
        
        public class ReportService {
            private Orders orders;
        
            public List<Order> findEligibleOrders() {
                return orders.findEligibleOrders();
            }
        }
        ```
        
        - 이렇게 되면 컬렉션과 관련된 응집도가 높아진다. → 데이터와 비즈니스 로직이 같이 있다.