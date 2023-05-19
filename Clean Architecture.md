# BookReview


### 서론

- 미리 알고 가야하는 사항
    - 이번에 이론적인 내용, 실습적인 코드를 경험하면서 작성한 내용입니다.
    - 저보다 이론적이고, 실습적인 부분은 다른 블로그 들이나 책에서 더 잘 정리가 되어 있지만, 그래도 제가 처음으로 직접 경험하고 느꼈던 생각과 궁금했던 점들을 적어 두도록 하겠습니다.
- 공부 방법
    - 나는 클린 아키텍처를 도입하기 전에 블로그에 있는 글도 많이 확인했는데 주로 두가지 책을 추천해 주었다.

            1. 만들면서 배우는 클린 아키텍처
            2. 클린 아키텍처

    - 두가지 책을 많이 추천해 주기에 서점에서 두가지를 보았는데 1번인 만들면서 배우는 클린 아키텍처가 입문용으로 좋아보여 1번 서적으로 공부를 하였습니다.
    - 추가적으로 블로그 참고를 많이 하였습니다.

### 본론

- Layered Architecture (계층형 아키텍처) 란?
    1. 계층형 아키텍처는 시스템을 여러 계층으로 구분하여 설계하는 방식이다.
    2. 각 계층은 특정한 기능이나 역할을 수행하며, 일반적으로 프레젠테이션 계층(웹), 비즈니스 계층(도메인), 데이터 액세스(영속성) 계층으로 구성된다.
    3. 계층 간의 의존성은 상위 계층에서 하위 계층으로 향한다.
        - 웹 → 도메인 → 영속성
    4. 계층 간의 분리와 재사용성을 강조합니다. 각 계층은 독립적으로 개발, 관리, 테스트될 수 있습니다.
    5. 위와 같은 장점이 있지만 계층형 아키텍처에도 단점은 존재한다. 
- Layered Architecture (계층형 아키텍처) 단점? (제가 경험한 것만 적어두겠습니다.)
    1. 데이터 베이스 주도 개발을 유도한다.
        
        ![image](https://github.com/taeyun1215/BookReview/assets/65766105/c1cc1e95-987f-4203-9158-3b1e8b619d0c)
        
        - 우리는 위와같이 계층형 아키텍처를 구현하지 않는가?
        - 이렇게 구현을 하였다면 영속성과 서비스에 강한 결합이 생긴다.
        - 서비스는 영속성 모델을 비지니스 모델처럼 사용하게 되고 이로 인해 도메인 로직뿐만 아니라 엔티티에 연관관계 매핑, 트랙잭션 관리 등 영속성에 관련된 로직도 처리를 해야한다.
    2. 테스트 하기가 어렵다.
        - 제가 예전에 봤던 강의에서는 일부러 Controller에 Repository를 주입해서 한번에 조회해서 응답을 해버리는 것도 봤었는데요, 이런 예외가 하나, 둘 허용되면 다음 두 가지 문제가 발생합니다.
        - 하나의 필드를 조작하는 것에 불과하더라도 도메인 로직을 웹 계층에서 구현하게 됩니다. 앞으로 유스케이스가 확장된다면 더 많은 도메인 로직을 웹 계층에 추가하여 전반적으로 계층간 책임이 섞이게되고, 핵심 도메인 로직들이 여기저기 흩어질 확률이 높아집니다.
        - 단위 테스트중 웹 계층 테스트에서 도메인 계층 뿐만 아니라 영속성 계층도 모킹해야한다는 것입니다. 규모가 커지면 다양한 영속성 컴포넌트에 의존성이 쌓이면서 테스트의 복잡도가 올라가고, 어느 순간에는 테스트 코드보다 모킹하는데 시간을 훨씬 더 많이 사용하게 됩니다.
    3. 실무적으로 동시에 작업하기가 어렵다.
        - 계층형 아키텍처는 controller, service, repository으로 크게 분류해서 작업하는데 하나의 기능 개발을 할 때 여러사람이 들어가서 작업하는 것이 많이 힘들다.
        - 어떤 사람은 controller을 작업하고 다른 사람은 service, 또 다른 사람은 repository만 작업하게 과연 가능할까? 가능은 하겠지만 사실 이렇게 작업하는 사람들은 거의 없을 것 이다.
        - 거의 모든 API는 영속성 계층을 의존하기 때문에 영속성 계층을 작업한 후, 도메인 계층, 웹 계층 순서대로 작업을 합니다. 이런 부분에서 한명의 개발자가 다 하는게 나은가 여러명의 개발자가 다 하는게 나은가는 재고해봐야할 문제가 아닐까 싶습니다.
        - 또한 서로 다른 usecase를 추가한다고 할 때 코드를 merge 하는 과정에서 conflict이 발생하기에 어려움을 느꼈습니다.
- Clean Architecture (클린 아키텍처) 사용하게 된 계기?
    - 계층형 아키텍처를 만들다가 어려웠던 점(위에서 작성한 계층형 아키텍처의 단점)을 보안하기 위해서 사용을 하였다.
    - ****우아콘2022에서 발표한 “기획자님들! 개발자가 아키텍처에 집착하는 이유, 쉽게 알려드립니다. “**** 를 보고 난 뒤 영감을 받아 도입하게 되었습니다.
        - 링크 : [https://www.youtube.com/watch?v=saxHxoUeeSw](https://www.youtube.com/watch?v=saxHxoUeeSw)
- Clean Architecture (클린 아키텍처) 란?
    - 
- Clean Architecture (클린 아키텍처) 돌아가는 로직은?
    
    ![image](https://github.com/taeyun1215/BookReview/assets/65766105/b21177c2-06e7-4eb9-af7b-dc585e96e16b)
    
    1. adapter(in → web)
    2. application(port → in)
    3. application(port → service)
    4. application(port → out)
    5. adapter(out → persistence)
    6. adapter(out → persistence → mapper) Domain → JPAEntity
    7. adapter(out → persistence)
    8. application(port → out)
    9. application(port → service)
    10. application(port → in)
    11. adapter(out → persistence → mapper) Domain → ResponseDTO
    12. adapter(in → web)
- 예시 코드 (User Register)
    - RegisterUserRequest.java
        
        ```java
        @Value
        @EqualsAndHashCode(callSuper = false)
        public class RegisterUserRequest extends SelfValidating<RegisterUserRequest> {
        
            @Pattern(
                    regexp = "^[a-z0-9]{4,20}$",
                    message = "아이디는 영어 소문자와 숫자만 사용하여 4~20자리여야 합니다."
            )
            @NotBlank(message = "아이디은 필수 입력 값입니다.")
            private String username;
        
            @Pattern(
                    regexp = "(?=.*[0-9])(?=.*[a-zA-Z])(?=.*\\W)(?=\\S+$).{8,16}",
                    message = "비밀번호는 숫자,문자,특수문자를 포함한 6~18로 입력해주세요."
            )
            @NotBlank(message = "비밀번호는 필수 입력 값입니다.")
            private String password;
        
            @NotBlank(message = "비밀번호 확인은 필수 입력 값입니다.")
            private String confirmPassword;
        
            @Size(min = 2, max = 12, message = "아이디는 2 ~ 12 사이로 입력해주세요.")
            @NotBlank(message = "닉네임은 필수 입력값입니다.")
            private String nickname;
        
            @Pattern(
                    regexp = "^(01[016789]{1})-?[0-9]{3,4}-?[0-9]{4}$",
                    message = "전화번호 형식이 올바르지 않습니다."
            )
            @NotBlank(message = "전화번호는 필수 입력값입니다.")
            private String phone;
        
            @Email
            @NotBlank(message = "이메일은 필수 입력값입니다.")
            private String email;
        
            public RegisterUserRequest(
                    String username,
                    String password,
                    String confirmPassword,
                    String nickname,
                    String phone,
                    String email
            ) {
                this.username = username;
                this.password = password;
                this.confirmPassword = confirmPassword;
                this.nickname = nickname;
                this.phone = phone;
                this.email = email;
                this.validateSelf();
            }
        
            public User toEntity(PasswordEncoder passwordEncoder) {
                return User.builder()
                        .username(username)
                        .password(passwordEncoder.encode(password))
                        .nickname(nickname)
                        .phone(phone)
                        .email(email)
                        .role(Role.USER)
                        .build();
            }
        
        }
        ```
        
        - 여기서 중점적으로 봐야할 것은 SelfValidating<RegisterUserRequest>이다.
        - 생성자를 통해 `this.validateSelf();` 사용하여 유효성 검사를 실행한다.
    - SelfValidating.java
        
        ```java
        public abstract class SelfValidating<T> {
        
            private Validator validator;
        
            public SelfValidating() {
                ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
                validator = factory.getValidator();
            }
        
            /**
             * Evaluates all Bean Validations on the attributes of this
             * instance.
             */
            protected void validateSelf() {
                Set<ConstraintViolation<T>> violations = validator.validate((T) this);
                if (!violations.isEmpty()) {
                    throw new ConstraintViolationException(violations);
                }
            }
        
        }
        ```
        
        - **객체의 유효성을 생성 시점에서 확실하게 보장하고 싶은 경우를 위해 사용한다.**
        - 해당 클래스에서 모든 Bean Validation 어노테이션을 평가하는 메서드입니다. **`validator`**를 사용하여 객체(**`this`**)의 속성에 대한 유효성을 평가하고, 어긴 항목이 있다면 **`ConstraintViolationException`** 예외를 throw합니다.
    - UserRegisterController .java
        
        ```java
        @RestController
        @RequiredArgsConstructor
        @RequestMapping("/users")
        public class UserRegisterController {
        
            private final RegisterUserUseCase registerUserUseCase;
            private final UserResponseMapper userResponseMapper;
        
            @PostMapping("/register")
            public ResponseEntity<ReturnObject> registerUser(
                    @RequestBody RegisterUserRequest registerUserRequest
            ) {
                User user = registerUserUseCase.registerUser(registerUserRequest);
                RegisterUserResponse response = userResponseMapper.mapToRegisterUserResponse(user);
        
                ReturnObject returnObject = ReturnObject.builder()
                        .success(true)
                        .data(response)
                        .build();
        
                return ResponseEntity.status(HttpStatus.OK).body(returnObject);
            }
        
        }
        ```
        
        - Controller에서는 UseCase를 받아서 사용한다.
        - 도메인 로직이 잘 돌아갔다면 Mapper를 이용하여 ResponseDTO로 변환해서 반환해준다.
        - ReturnObject는 API Repsonse을 더 깔끔하게 보여주기 위해 작성한 객체입니다.
    - RegisterUserUseCase.java
        
        ```java
        public interface RegisterUserUseCase {
            User registerUser(RegisterUserRequest registerUserRequest);
        }
        ```
        
        - 인터페이스로 작성하여 Service에서 UseCase를 implements하여 사용한다.
    - RegisterUserService .java
        
        ```java
        @Slf4j
        @UseCase
        @Transactional
        @RequiredArgsConstructor
        public class RegisterUserService implements RegisterUserUseCase {
        
            private final SaveUserStatePort saveUserStatePort;
            private final PasswordEncoder passwordEncoder;
        
            @Override
            @Transactional
            public User registerUser(RegisterUserRequest registerUserCommand) {
                if (!Objects.equals(registerUserCommand.getPassword(), registerUserCommand.getConfirmPassword())) {
                    throw new RuntimeException("두개의 비밀번호가 맞지 않습니다.");
                }
        
                User saveUser = registerUserCommand.toEntity(passwordEncoder);
                saveUserStatePort.saveUser(saveUser);
        
                return saveUser;
            }
        
        }
        ```
        
        - Usecase의 구현체인 Service에서 비즈니스 로직을 처리한다.
        - 아웃고잉 포트인 SaveUserStatePort를 호출한다.
        - `User saveUser = registerUserCommand.toEntity(passwordEncoder);` → 여기는 어떻게 해결해야할지 생각중이다. 분명 user로 바꿔주고 Port에 넣어주는게 맞다고 생각하지만 변환하는 과정이 저렇게 해도 되는지는 의문이다.
    - SaveUserStatePort.java
        
        ```java
        public interface SaveUserStatePort {
            void saveUser(User user);
        }
        ```
        
    - UserPersistenceAdapter.java
        
        ```java
        @RequiredArgsConstructor
        @PersistenceAdapter
        public class UserPersistenceAdapterState implements SaveUserStatePort {
        
            private final UserJpaRepo userJpaRepo;
            private final UserPersistenceMapper userPersistenceMapper;
        
            @Override
            @Transactional
            public void saveUser(User user) {
                userJpaRepo.save(userPersistenceMapper.mapToJpaEntity(user));
            }
        }
        ```
        
- 궁금했던 점
    1. DTO는 어느 위치에 두어야하는지?
    2. Controller도 API 별로 나눠서 클래스를 만들어야하는지?
    3. Mapper는 Persistence와 Response를 따로 만들어서 사용하는?
    4. 패키지 구조중 Application → port → in 에서 파일 명이 UseCase와 Query의 차이점은?
    5. Domain Entity와 JPA Entity의 연관관계의 차이점은?
    6. 5번 질문 추가사항으로 Domain Entity은 어떻게 두어야지 순환참조에 안 걸릴까?
    7. DTO와 Command의 차이점?
    8. StatePort, Port의 차이점?
    9. PersistenceMapper는 어디까지 해줘야하는가?

### 결론
