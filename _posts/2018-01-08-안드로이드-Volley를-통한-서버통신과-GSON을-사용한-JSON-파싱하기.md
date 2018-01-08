---
layout: post
title: "안드로이드 Volley를 통한 서버 통신과 GSON을 사용한 JSON 파싱하기"
author: "Hyemin"
---

<style>
  img {
    margin: auto;
  }
</style>

안드로이드 개발자라면 당연히 할 수 있어야 하는 것들이 있다. 오늘은 그 중 두 가지인 **서버와의 통신과, JSON 파싱하기** 를 다뤄보려고 한다. <s>본인도 지난 주 토요일에 배웠다는 게 함정</s> 그리고 그 전에 먼저 Volley, JSON, GSON이 각각 무엇인지 알아보자.

### Volley
2013년 구글에서 발표한 안드로이드 HTTP 라이브러리이다. 안드로이드 애플리케이션에서 1. 서버에 데이터 요청을 전송하고(serialization) 2. 서버에서 제공하는 데이터를 읽어 3. 데이터를 디코딩해(deserialization) UI에서 보여주는 일련의 과정은 간단해 보이는 패턴인데, 의외로 이 과정을 구현하기 위해 신경 써야 하는 부분은 적지 않다. 자세한 설명은 [Volley 설명](https://gist.github.com/benelog/5981448)을 참조하면 되겠다. 간단히 이야기 하자면 Volley는 서버와의 통신 기능을 제공하면서, 그 과정에서 발생하는 복잡함을 해결해주는 라이브러리라고 할 수 있다.

### JSON(JavaScript Object Notation)
경량의 DATA-교환 형식이다. 지정된 포맷으로 데이터가 구조화 되어있기 때문에 다양하게 이용된다. 좀 더 자세한 설명은 [JSON 개요](https://www.json.org/json-ko.html)를 참조하라.

### GSON
이름을 통해 짐작했겠듯 JSON과 연관이 있는 라이브러리이다. JSON은 분명 좋은 데이터 포맷을 갖추고 있지만, 이 데이터를 그대로 파싱하는 일은 귀찮고 지저분하다(코드가). GSON은 자바 오브젝트를 JSON 형식으로 변환하거나, 반대로 JSON을 자바 오브젝트로 변환하는 일을 간단하게 만들어준다. [google-gson](https://github.com/google/gson)

이제 각각에 대한 설명을 마쳤으니 이를 사용한 간단한 실습을 해보자. Volley, GSON, Google Map Directions API를 사용해 [애플에서 구글로 가는데 걸리는 가장 빠른 주행 시간](https://www.google.co.kr/maps/dir/Apple+Infinite+Loop,+One+Infinite+Loop,+Cupertino,+CA+95014+%EB%AF%B8%EA%B5%AD/%EA%B5%AC%EA%B8%80+%EB%AF%B8%EA%B5%AD+94043+California,+Mountain+View,+Amphitheatre+Pkwy/@37.3777539,-122.0960612,13z/data=!3m1!4b1!4m14!4m13!1m5!1m1!1s0x808fb5b6c4951d0f:0xb651414deb31e9fb!2m2!1d-122.030189!2d37.3316756!1m5!1m1!1s0x808fba02425dad8f:0x6c296c66619367e0!2m2!1d-122.0840575!2d37.4219999!3e0?hl=ko)을 뽑아 화면에 출력하자. 참고로 사용할 언어는 코틀린이다.

* 먼저 [Google Maps API](https://developers.google.com/maps/documentation/directions/?hl=ko) 사용을 위해 구글맵 키 값을 받아온다.

* 구글맵에서 자동자 주행기준 이동 시간을 얻기 위해, 베이스url+목적지+도착지+키값의 인코딩 형식에 맞춰 url을 저장한다.

{% highlight linenos%}
Base url:
https://maps.googleapis.com/maps/api/directions/json?
Origin: origin=75+9th+Ave+New+York,+NY
Destination:  destination=MetLife+Stadium+1+MetLife+Stadium+Dr+East+Rutherford,+NJ+07073
Key: key=YOUR_API_KEY
{% endhighlight %}

* 이제 Volley를 사용할 시점이다. build.gradle 파일의 dependencies에 다음과 같이 코드를 추가하자.

{% highlight linenos %}
dependencies {
  compile 'com.android.volley:volley:1.1.0'
}
{% endhighlight %}

* Volley를 통해 JSON 데이터를 읽어 화면에 출력하는 코드를 작성한다.

{% highlight scala linenos %}
class MainActivity : AppCompatActivity() {
    private lateinit var queue: RequestQueue
    private val url = YOUR_URL

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val textView: TextView = main_result_txt
        queue = Volley.newRequestQueue(this)

        val stringRequest = StringRequest(Request.Method.GET, url,
                Response.Listener<String> { response -> textView.text = response},
                Response.ErrorListener { textView.text = getString(R.string.main_error_listener) })

        stringRequest.tag = TAG
        queue.add(stringRequest)
    }

    override fun onStop() {
        super.onStop()
        queue.cancelAll(TAG)
    }
}
{% endhighlight %}

* GSON을 사용해 파싱을 할 차례이다. 다음 코드를 build.gradle에 추가하자

{% highlight linenos %}
dependencies {
  compile 'com.google.code.gson:gson:2.8.2'
}
{% endhighlight %}

* JSON 데이터를 객체로 변환하기 위해 클래스 템플릿을 작성한다.이때 변수의 이름을 JSON의 name과 같게 설정하거나, @SerializedName 어노테이션을 사용한다.

{% highlight scala linenos %}
class GoogleMapAPIModel {
    lateinit var routes: ArrayList<Route>

    class Route {
        lateinit var legs: List<Leg>

        class Leg {
            lateinit var duration: Duration

            class Duration {
                lateinit var text: String
            }
        }
    }
}
{% endhighlight %}

* 이제 만들어 놓은 클래스와 GSON을 활용해 원하는 값을 출력할 수 있다.
{% highlight scala linenos %}
{ response ->
                    val gson = Gson()
                    val sample = gson.fromJson(response, GoogleMapAPIModel::class.java)
                    textView.text = sample.routes[0].legs[0].duration.text
}
{% endhighlight %}
