---
layout: post
title: TDD(Test-Driven Development) (1)
author: Hyemin
---

<style>
  img {
    margin: auto;
  }
</style>

여름방학부터 안드로이드를 학습할 겸, 동기들과 'I Time U'라는 포모도로(pomodoro) 앱을 만드는 프로젝트가 막바지에 다다랐다. 이 프로젝트를 통해 배운 점에 대해서는 머지 않아 포스팅하겠지만, 가장 크게 와닿은 것 중 하나는 바로 오늘의 주제인 TDD(Test-Driven Development)의 중요성이었다.

TDD를 사용하지 않은 이번 프로젝트에서의 한계는 명확했다. 테스트 코드를 작성하지 않으니 하나의 함수에 여러 기능이 들어가고, 그에 더해 미처 고려하지 못한 변인들로부터 버그가 발생했다.

물론 개발을 하며 버그가 생기는 건 당연한 현상이다. 문제는 그렇게 생긴 버그가 한 두 가지가 아니며, 그것을 찾고 픽스하는데에 걸린 시간이 적지 않았던데다, 결론적으로 기껏 힘들게 버그를 고치고 완성한 코드가 스파게티(!)의 형상이었다는 점이다.

![spagetti](/images/2017-09-10-TDD(Test-Driven-Development)-(1)/spagetti.jpg "스파게티는 맛있기라도 하지...")

TDD의 필요성을 절감했으니 다음은 TDD에 대해 배우고 정리하는 단계이다. 학습을 위해 [Microsoft Virtual Academy: Test-Driven Development](https://mva.microsoft.com/en-US/training-courses/testdriven-development-16458) 영상을 참고했다. 선행 과정은 딱히 없고, 프로그래밍의 기본 구조를 알고 있다면 이해하기가 그리 어렵지 않다. 언어가 영어로만 제공된다는 점에서 약간의 장벽은 있을 수 있지만, 자막을 보면서 천천히 따라가보면 이해할 수 있으리라 생각한다.

이하는 코스의 첫 번째 ~ 세 번째 모듈을 듣고 이해한 것을 정리한 글이다.<br><br>

## Test-Driven Development in a Nutshell

TDD는 테스트를 개발 프로세스에 통합시키는 것을 의미한다. 개발자는 코드가 **하게 될** 것에 대해 테스트 코드를 정의하고, 해당 테스트 코드를 통과하게 하기 위한 코드를 작성한다. 다만 주의할 점은 테스트를 통과하기 위한 것과 무관한 코드를 작성하지 않아야 한다는 것이다. 코드 작성에 대한 규칙은 다음과 같다.

* 자동화된 테스트에 실패할 경우에만 코드를 작성한다.
* 중복을 제거한다.

프로그래밍 과정은 아래 그림과 같이 순환구조를 거친다.

<img src="/images/2017-09-10-TDD(Test-Driven-Development)-(1)/red-green-refactor.png" alt="red-green-refactor"/>

이미지에 나타난 절차는 아래의 5가지 순서로 세분화 할 수 있다.

1) 테스트 코드를 추가한다.<br>
2) 테스트를 실행하여 새로운 실패를 확인한다.<br>
3) 코드를 약간 수정한다.<br>
4) 모든 테스트를 통과해 전체 테스트의 성공을 확인한다.<br>
5) 중복된 부분을 제거하여 리팩토링한다.

언뜻 보기엔 TDD가 비효율적으로 여겨질 수 있다. 테스트 코드를 작성하는 것도 귀찮고, 일부러 테스트 통과를 하지 말라고 하며, 코드 수정도 테스트를 통과할 정도로만 조금씩하니 답답할만도 하다.

그러나 역설적이게도, 단점으로 여겨지는 그 점들은 TDD의 이점으로 작용한다.

* 테스트를 하기 때문에 코드에 대한 신뢰도가 높아진다.
* 테스트를 통해 원하는 결과를 도출하는 코드를 작성함으로써 설계 의도에 보다 가까운 개발을 할 수 있다.
* 테스트 통과를 실패함으로써 자칫 놓칠 수 있는 버그를 찾거나 고치는 걸 일찍 할 수 있다.
* 피드백을 받는 주기가 짧아 결과로 예쁜 코드(clean code)를 얻을 수 있다.
<br><br>

## Types of automated tests

자동화 테스트의 종류와 그에 대해 필자가 이해한 정의는 다음과 같다.

* Unit tests: 기능을 작은 단위로 쪼개서 테스트를 진행하고, 해당 코드가 예상대로 작동하는지 확인한다.
* Integration tests: 두 개 이상의 종속 소프트웨어 모듈을 다양한 방법으로 그룹화하여 테스트한다.
* User Interface(UI) tests: 어플리케이션 UI를 테스트해 사양을 검증한다.
* Web performance tests: 애플리케이션의 응답성, 처리량, 신뢰성과 확장성을 테스트한다.
* Load tests: 시스템이 다양한 부하 수준에서의 동작을 결정하고, 병목현상을 확인하며, 기대하는 동작을 수행하는지 테스트한다.
<br><br>

## Unit testing and Test-Driven Development

유닛 테스트는 개발을 하면서 가장 많이 작성하게 될 부분이다. 아래는 C#으로 작성된 유닛 테스트 코드의 양식이다.

{% highlight cs linenos %}
[TestMethod()]
public void whenXHappens_GivenY_ResultShouldBeZ() {
  // arrange
  Thing newThing = new Thing();
  // act
  newThing.doSomething();
  // assert
  Assert.AreEqual("Actual", newThing.toString());
}
{% endhighlight %}

1: 테스트 메소드(method)임을 확인한다.<br>
2: 메소드의 이름<br>
3-4: 테스트를 위한 변수와 오브젝트를 구성한다.<br>
5-6: 테스트를 동작한다.<br>
7-8: 예상 결과가 발생했는지 확인한다.<br>

유닛 테스트는 세 가지 조건을 만족해야 한다.

* Automatic: 테스트 결과를 스스로 체크해야 한다.
* Repeatable: 여러 사람들에 의해 반복적으로 실행될 수 있어야 한다.
* Available: 해당 코드를 가지고 있는 사람은 누구든지 테스트를 실행할 수 있어야 한다.

조건을 만족하는 유닛 테스트는 TDD의 장점을 가지며, 코드가 실제로 어떻게 동작하는지 알려주는 문서의 역할을 한다.
