# Django Topics

> Django에서 다루는 재밌는 주제들을 모아놓은 저장소입니다.


### 1. 모델 메서드 오버라이딩 vs signals

[Django 공식 문서 관련 글](https://docs.djangoproject.com/en/dev/topics/db/models/#overriding-predefined-model-methods) / [stack overflow의 관련 질문](https://stackoverflow.com/questions/170337/django-signals-vs-overriding-save-method)

보통 signals 을 사용하게 되면 유지보수가 힘들어져서 사용을 꺼리지만, 제대로 사용하면 오히려 간단해질 수도 있다.<br/>
signals을 사용하면 좋은 경우는 해당 모델 외의 관계된 다른 모델에도 변경을 줄 때 효과적이다.<br/>
메서드 오버라이딩을 통해 작업하는 게 좋은 경우는 해당 메서드가 있는 모델에만 변경을 줄 때이다.<br/>
그리고 모델 메서드를 오버라이딩 하는 경우, `bulk operations` 실행 시에는 오버라이딩한 모델 메서드가 실행되지 않는다.<br/>
이런 경우에는 signals 사용을 피할 수 없다.


### 2. Class-based View vs Funtion-based View

[관련 글](https://simpleisbetterthancomplex.com/article/2017/03/21/class-based-views-vs-function-based-views.html)

각각 장단점이 명확하다.

**Funtion-based View**

- 장점
    - 코드 흐름이 쉽고, 명확하다.
    - 데코레이터 사용이 쉽다.
    - 구조가 단순하여 읽기 쉽다.

- 단점
    - CBV에 비해 타이핑 수가 많다.
    - 코드의 재사용이 어렵다.
    - HTTP 메서드에 따라 처리하기가 깔끔하지 않다.


**Class-based View**

- 장점
    - FBV에 비해 타이핑 수가 적다.
    - 코드 확장이 쉽다.
    - 제너릭 뷰, 믹스인 등을 통해 코드 재사용이 용이하다.
    - 별도의 메서드로 HTTP 메서드에 따라 핸들링하기 편하다.

- 단점
    - 코드 흐름이 명확하지 않다.
    - 데코레이터를 사용하려면 추가 처리가 필요하다.


### 3. User 모델 커스터마이징
> 크게는 [customizing authentication](https://docs.djangoproject.com/en/2.0/topics/auth/customizing/) 이라는 주제로 공식 문서에서 다루고 있다.

서비스를 하다보면 Django에서 기본으로 제공해주는 User 모델로는 서비스의 비즈니스 로직을 수행하기 어려운 부분이 있다.<br/>
해서 Django를 쓰는 대부분의 기업에서는 User 모델을 커스터마이징 하고 있지 않을까 생각한다.<br/>
이 방법은 크게 2가지로 나뉘는 걸로 알고 있다. (더 있다면 이슈로 알려주세요.)

1. **Django의 User 모델과 1:1 관계를 가진 User 모델 만들기** ([공식 문서](https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#extending-the-existing-user-model))
2. **AbstractBaseUser 혹은 AbstractUser를 상속받는 User 모델 만들기** ([공식 문서](https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#substituting-a-custom-user-model))

2개의 경우가 각각 사용되는 경우가 다르다.<br/>
자신이 사용하려는 목적에 맞는 방법을 선택하면 될 것 같지만, **굳이** 비교를 해보자면

1번의 경우, 간단하고 적은 코드로 Django의 User 모델을 확장하여 사용할 수 있다.<br/>
2번의 경우, `AbstractBaseUser` 모델을 상속한 User 커스텀 모델을 만들면 로그인 아이디로 이메일 주소를 사용하거나 Django 로그인 절차가 아닌 다른 인증 절차를 직접 구현할 수 있다.<br/>
