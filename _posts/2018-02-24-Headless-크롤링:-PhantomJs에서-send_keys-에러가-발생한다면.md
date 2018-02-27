---
layout: post
title: "Headless 크롤링: PhantomJs에서 send_keys 에러가 발생한다면"
author: "Hyemin"
---

웹 서버에 저장할 데이터를 저장하기 위해 파이썬으로 크롤러를 돌리던 중, 코드가 Chrome에서는 작동하나 PhantomJs에서는 작동하지 않음을 알게 되었다. 에러의 세부사항은 다음과 같았다.

{% highlight py3tb %}
Traceback (most recent call last):
  File "SnoweCrawler.py", line 116, in <module>
    crawler.set_info('user_id', 'password')
  File "SnoweCrawler.py", line 24, in set_info
    self.browser.find_element_by_id('userId').send_keys(user_id)
  File "/usr/local/lib/python3.5/dist-packages/selenium/webdriver/remote/webelement.py", line 479, in send_keys
    'value': keys_to_typing(value)})
  File "/usr/local/lib/python3.5/dist-packages/selenium/webdriver/remote/webelement.py", line 628, in _execute
    return self._parent.execute(command, params)
  File "/usr/local/lib/python3.5/dist-packages/selenium/webdriver/remote/webdriver.py", line 312, in execute
    self.error_handler.check_response(response)
  File "/usr/local/lib/python3.5/dist-packages/selenium/webdriver/remote/errorhandler.py", line 242, in check_response
    raise exception_class(message, screen, stacktrace)
selenium.common.exceptions.InvalidElementStateException: Message: {"errorMessage":"Element is not currently interactable and may not be manipulated","request":{"headers":{"Accept":"application/json","Accept-Encoding":"identity","Connection":"close","Content-Length":"164","Content-Type":"application/json;charset=UTF-8","Host":"127.0.0.1:33770","User-Agent":"Python http auth"},"httpVersion":"1.1","method":"POST","post":"{\"id\": \":wdc:1519469631993\", \"sessionId\": \"faa53810-1950-11e8-a9e6-3b37b157e7cf\", \"text\": \"user_id\", \"value\": [\"r\", \"i\", \"d\", \"d\", \"l\", \"e\", \"1\", \"1\", \"1\", \"7\"]}","url":"/value","urlParsed":{"anchor":"","query":"","file":"value","directory":"/","path":"/value","relative":"/value","port":"","host":"","password":"","user":"","userInfo":"","authority":"","protocol":"","source":"/value","queryKey":{},"chunks":["value"]},"urlOriginal":"/session/faa53810-1950-11e8-a9e6-3b37b157e7cf/element/:wdc:1519469631993/value"}}
Screenshot: available via screen
{%endhighlight%}

로그가 길긴 하지만 찬찬히 살펴보면, 로그인 폼에 아이디와 비밀번호를 보내는 코드의 위치에서 에러가 나타나고 있었다. 그 중에서도 send_keys라는 함수의 문제인 듯 했다. 문제의 코드는 다음과 같았다.

{% highlight python linenos%}
def set_info(self, user_id, password):
    self.browser.get('https://snowe.sookmyung.ac.kr/bbs5/users/login')
    time.sleep(3)
    self.browser.find_element_by_id('userId').send_keys(user_id)
    self.browser.find_element_by_id('userPassword').send_keys(password)
    time.sleep(5)
    self.browser.find_element_by_id('loginButton').send_keys(Keys.ENTER)
    self.browser.implicitly_wait(3)
    return
{% endhighlight %}

하지만 앞서 언급했듯 크롬에서는 문제가 없었다. PhantomJS에서 크롬과 화면이 다른가 싶어 스크린샷도 찍어보고, 페이지 요소가 모두 로드되지 않아 문제가 발생하는가 싶어 sleep에 시간을 추가해봐도 결과는 마찬가지였다. 그리고 구글링을 통해 얻은 결과가 다음과 같다.

[The solution is we need to set a fake browser size before doing browser.get("")](https://github.com/ariya/phantomjs/issues/11637)

원하는 페이지를 get으로 부르기 전에 페이크 브라우저를 하나 부르는 것이다. 이를 적용해 수정한 코드는 다음과 같다.

{% highlight python linenos%}
def set_info(self, user_id, password):
    self.browser.set_window_size(1124, 850)
    self.browser.get('https://snowe.sookmyung.ac.kr/bbs5/users/login')
    time.sleep(3)
    self.browser.find_element_by_id('userId').send_keys(user_id)
    self.browser.find_element_by_id('userPassword').send_keys(password)
    time.sleep(5)
    self.browser.find_element_by_id('loginButton').send_keys(Keys.ENTER)
    self.browser.implicitly_wait(3)
    return
{% endhighlight %}
