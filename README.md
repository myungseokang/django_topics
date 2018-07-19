# Django Topics

> Django에서 다루는 재밌는 주제들을 모아놓는 저장소입니다.


### 1. 모델 메서드 오버라이딩 vs signals

[Django 공식 문서 관련 글](https://docs.djangoproject.com/en/dev/topics/db/models/#overriding-predefined-model-methods)
[stack overflow 관련 글](https://stackoverflow.com/questions/170337/django-signals-vs-overriding-save-method)

보통 signals 을 사용하게 되면 유지보수가 힘들어져서 사용을 꺼리지만, 제대로 사용하면 오히려 간단해질 수도 있다.

signals을 사용하면 좋은 경우는 해당 모델 외의 관계된 다른 모델에도 변경을 줄 때 효과적이다.

메서드 오버라이딩을 통해 작업하는 게 좋은 경우는 해당 메서드가 있는 모델에만 변경을 줄 때이다.

그리고 모델 메서드를 오버라이딩 하는 경우, `bulk operations` 실행 시에는 오버라이딩한 모델 메서드가 실행되지 않는다.

이런 경우에는 signals 사용을 피할 수 없다.
