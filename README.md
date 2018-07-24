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

### 4. QuerySet이 계산(evaluate)되는 경우

> 공식 문서에서도 [When QuerySets are evaluated](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#when-querysets-are-evaluated) 라는 이름으로 다루고 있다.

내부적오르 QuerySet은 직접적으로 데이터베이스에 도달하지 않고, 웬만한 작업을 실행한다.<br/>
QuerySet이 계산되기 전까지 실제로 데이터베이스에 접근하지 않는다.<br/>
하지만 사용하다보면 QuerySet을 계산할 필요가 없는 부분에서도 계산돼서 데이터베이스에 접근을 여러 번 하게 되는 경우가 발생한다.<br/>
실제로도 겪었었고, 그 때 QuerySet은 게으르게(lazy) 계산된다는 것을 알았다.<br/>
그래서 QuerySet이 계산되는 경우를 정리해보고자 한다.<br/>

아래는 QuerySet이 계산되는 경우이다.

1. 반복 (Iteration): QuerySet은 반복 가능 (Iterable)하고, 처음 반복할 때 QuerySet이 계산된다.

2. 슬라이싱 (Slicing): QuerySet도 Python의 리스트처럼 슬라이싱이 가능하다. 대부분의 경우에는 계산되지 않은 QuerySet을 슬라이싱하면 다른 계산되지 않은 QuerySet을 반환한다. 하지만 슬라이싱할 때 `step` 인자를 사용하게 되면 QuerySet이 게산된다. `step` 인자는 다음과 같이 사용된다.
```
>>> temp_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> temp_list[0:9:2]
[1, 3, 5, 7, 9]
```

`temp_list[start:end:step]`의 경우 `start`부터 `end`까지 `step`만큼 증가시키면서 슬라이싱한다.

공식 문서에도 다루고 있다 ([Limiting QuerySets](https://docs.djangoproject.com/en/2.0/topics/db/queries/#limiting-querysets)).

3. 피클링 (Pickling) / 캐싱 (Caching)

4. repr(): `repr()`를 호출할 떄도 계산된다. 이건 Python 인터프리터에서의 편의를 위해서 제공된다.

5. len(): `len()`를 호출할 때도 계산된다. 만약 실제 인스턴스가 필요하지 않고 개수만 필요가 있다면 `len()`보다는 `count()`를 사용하는 게 훨씬 효율적이다.

6. list(): `list()`를 호출해서 QuerySet을 강제로 계산할 수도 있다.

7. bool(): `bool()`이나 `or`, `and` 혹은 `if`문과 같은 불리언 (Boolean) 컨텍스트에서 QuerySet을 사용하는 경우에도 평가된다. 결과가 하나 이상 있으면 `True`이고, 아니면 `False`이다. 만약 적어도 하나 이상의 결과가 있는지 확인하려는 것이라면 `exists()`를 사용하는 것이 더 효율적이다.
