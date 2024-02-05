
### 9. 설계의 건전서을 해치는 여러 악마

- YAGNI 원칙
    - YAGNI(You Aren’t Gonna Nedd It)은 지금 필요 없는 기능을 만들지 말라 입니다.
    - 왜냐하면 소프트웨어에 대한 요구사항은 매일매일 변화한다. 사양으로 확정되지 않고 명확하게 언어화되지 않은 요구를 미리 예측하고 구현해도, 이러한 예측은 대부분 맞지 않는다.
    - 예측에 들어맞지 않는 로직은 데드 코드가 된다.
    - 또한, 가독성을 낮추고 읽는 사람을 혼란스럽게 만든다.
- null 문제
    - null로 객체를 초기화 시킨다면, 여러 군데에서 null을 체크해야하는 일이 발생한다. → 가독성이 떨어집니다.
    - 또한 실수로 null을 체크 안 하는 곳이 생길 수도 있다. → 에러 발생
    - 따라서, null을 리턴하지 않는 설계, null을 전달하지 않는 설계를 해야한다.
- null 안전
    - null 안전이란 null에 의한 오류가 아예 발생하지 않게 만드는 구조이다.
    - null 안전 예시
        
        ```java
        String name1 = "John";
        Optional<String> opt1 = Optional.ofNullable(name1); // opt1은 "John" 값을 포함하는 Optional이다.
        
        String name2 = null;
        Optional<String> opt2 = Optional.ofNullable(name2); // opt2는 값이 없는 Optional로, Optional.empty()와 같다.
        ```
        
- 예외를 Catch 하고서 무시하는 코드
    - 코드에 오류가 났는데도 오류를 탐지할 방법이 없어진다.
    - 어느 시점에서 어떤 코드에서 문제가 발생했는지 찾기가 힘들어진다.
    - 안 좋은 예시
        
        ```java
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
        
        }
        ```
        
    - 좋은 예시
        
        ```java
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            // 1. 로그 남기기
            System.err.println("An error occurred: " + e.getMessage());
        
            // 2. 사용자에게 의미 있는 메시지 표시
            System.out.println("You tried to divide by zero. Please provide a valid denominator.");
        
            // 3. 프로그램의 흐름 조정
            // 예를 들면, 다시 입력을 받는다거나 기본값을 설정할 수 있다.
        }
        ```
        
        - 문제가 생겼다면 Catch에서 통지 및 기록을 해두는것이 좋다.
- Sliver Bullet
    - Sliver Bullet이란? 울버린, 늑대인간, 드라큘라와 같은 괴물들에게 은탄환(one seeks bullets of silver)은 한번에 무력화 시킬 수 있는 최고의 도구이다.
    - [1986년 프레드 브룩스가 쓴 소프트웨어 공학 논문](http://worrydream.com/refs/Brooks-NoSilverBullet.pdf)에서 은탄환의 존재에 대해 최초로 언급하였다. 이와 동시에 소프트웨어 개발의 복잡성을 한번에 해소할 마법같은 솔루션(은탄환)은 없다고 선언한다.
    - 우리의 설계에는 Best라는 것은 없다. 항상 Better을 목표로 할 뿐이다.
        - 항상 제일 좋은 코드가 무엇인가를 생각하면서 짰었는데, 나의 입장에서 최대한 한 발자국이라도 더 나아갔다면 그걸로 ok…입니다…