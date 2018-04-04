---
layout: post
title: "서버에서 PhantomJS를 활용해 웹 크롤링 시 프로세스가 사라지지 않는 현상 해결방법"
author: "Hyemin"
---

<style>
  img {
    margin: auto;
  }
</style>

어느 순간부터 서버가 급격히 느려지는 현상이 발생했다. 일시적인 현상이었다면 몰라도 모바일에서 서버에 접근이 불가할 정도로 서버가 먹통이니, 큰 문제가 아닐 수 없었다. 임시 방편으로 서버를 멈췄다가 다시 시작하면 서버가 원활하게 작동하지만 영구적인 해결 방안은 아니었다. 다만 이를 통해 서버 자체에 문제가 있다고 하기 보다는 다른 부분에서 문제가 발생한다는 사실을 짐작할 수 있었다.

문제를 해결하기 위해 ps -e 명령어로 프로세스 목록을 살펴보니 처음에는 필요한 프로세스들만 나타났다. 하지만 서버가 점점 느려지기 시작할 때 프로세스 목록을 살펴보니 phantomjs 프로세스가 화면에 가득한 걸 볼 수 있었다. <s>잡았다</s>

<img src="/images/2018-04-04/podol.jpg"/>

우분투 기반 서버에서 crontab을 사용해 파이썬 웹 크롤러를 돌리고 있었는데, 그것이 문제였던 셈이다. <s>크롤러를 없애버릴 수도 없고</s> 본인은 분명 quit()을 사용해 브라우저를 종료해줬는데, 왜 그런가 하고 찾아봤더니 PhantomJS 자체의 버그라는 결론을 얻을 수 있었다.

본인만 이런 문제를 겪은 건 아닌지, 검색 결과가 꽤 많았고 해결방안 또한 존재했다.

[Python/Linux quit() does not terminate PhantomJS process](https://github.com/seleniumhq/selenium/issues/767)

답변은 다음과 같았다. 코드 상에서 시그널 종료 신호를 보내 프로세스를 멈추도록 하는 전략을 사용한 것이다.

{% highlight lineos %}
browser.service.process.send_signal(signal.SIGTERM)
browser.quit()
{% endhighlight%}

그러나 Phantomjs라는 이름의 프로세스는 여전히 사라지지 않았다. (솔직히 될 것 같은데 왜 안 되는 건지는 여전히 잘 모르겠다. 아시는 분은 코멘트를 달아주시면 감사하겠다.) 다만 검색을 하다가 스스로 떠올린 방안이 따로 있었기에 그 방법을 시도해보기로 했다.

서버에서는 crontab을 이용해 크롤러를 돌릴 때 shell 파일을 사용하고 있었고, 본인은 그 점을 이용해 모든 크롤러 코드 실행 명령어를 작성한 후 **pkill phantomjs** 명령어를 덧붙여줬다.

{% highlight lineos %}
./global_notice.py
./wiz_crawler.py
./Wiz5DepartmentsCrawler.py
./SnoweCrawler.py
./Bayesian_Classifier.py
pkill phantomjs
{% endhighlight%}

이후 phantomjs 프로세스가 잔존하는 일은 없어졌고 서버가 느려지는 문제는 해결되었다!
