### 2. 설계 첫 걸음

- 의도를 분명히 전달할 수 있는 이름 설계하기
- 변수를 계속해서 재할당 하는것이 아닌 목적별로 변수를 따로 생성하기
    - 가독성 저하: 변수의 값이 여러 번 바뀌면 코드를 읽는 사람이 그 변수의 현재 값이 무엇인지 추적하기 어려워진다.
    - **불변성 손실**: 불변성(Immutability)은 데이터의 안정성을 보장하고 함수형 프로그래밍 패러다임에서 중요하게 여겨진다. 재할당을 많이 사용하면 불변성을 유지하기 어려워진다.
    - **리팩토링 어려움**: 변수가 재할당되는 코드는 리팩토링이나 코드의 수정이 필요할 때 변경의 범위를 예측하기 어려워진다.
- 데이터와 로직이 묶여 있는 좋은 클래스
    
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
    
        // 해당 품목의 총 가격을 계산
        public double calculateTotalPriceForItem() {
            return price * quantity;
        }
    }
    ```
    
    - 생성자를 통해 유효성 검사를 완료하였다.
    - `calculateTotalPriceForItem` → 메소드 명만 확인해도 해당 품목의 총 가격을 계산하는 로직인 걸 알수 있다.