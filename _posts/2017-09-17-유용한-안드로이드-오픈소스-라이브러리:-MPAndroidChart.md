---
layout: post
title: "유용한 안드로이드 오픈소스 라이브러리: MPAndroidChart"
author: "Hyemin"
---

<style>
  img {
    margin: auto;
  }
</style>

오늘 소개할 라이브러리는 화면에 차트를 쉽게 그릴 수 있도록 돕는 오픈소스이다. 텍스트로 이루어진 데이터의 나열보다는 시각적인 그래프가 필요할 때가 있다. 가령 사용자에게 어떤 일에 대한 통계를 보여주는 경우, 이 오픈소스는 당신에게 큰 도움이 될 것이다.

![MPAndroidChart](/images/2017-09-17-유용한-안드로이드-오픈소스-라이브러리:-MPAndroidChart/MPAndroidChart.png)

> 이 오픈소스 역시 진행 중인 프로젝트에 큰 도움을 준 라이브러리이다. :)

MPAndroidChart에서 제공하는 차트는 line chart, bar chart, combined-chart(line + bar), horizontal-bar chart, pie chart, scatter chart, candle stick chart, bubble chart, 그리고 radar chart(이미지는 링크를 통해 깃허브 페이지로 이동하면 확인할 수 있다)로 다양한 종류가 구비되어 있다.

[MPAndroidChart: A powerful Android chart](https://github.com/PhilJay/MPAndroidChart)

다만 차트의 데이터를 동적으로 할당할 경우엔 난이도가 좀 있는 편으로 여겨진다. 필자와 같이 안드로이드로 처음 프로젝트를 진행해보는 사람이라면 꽤 애를 먹을지도 모른다. 물론 [위키](https://github.com/PhilJay/MPAndroidChart/wiki)가 잘 되어 있으므로, 안드로이드 개발에 친숙한 사람이라면 큰 어려움 없이 개발할 수 있겠지만 말이다.

이해를 돕기 위해 설명을 첨부한다. 개발 시 **라이브러리의 버전은 3.0**, 차트는 **line chart** 를 사용했다. 필자가 만든 것은 Week/Month/Custom 의 기간에 따라 데이터를 그래프로 출력하는 통계 페이지였다.

![통계 예제](/images/2017-09-17-유용한-안드로이드-오픈소스-라이브러리:-MPAndroidChart/statistics.png)

<br><br>
어려움을 겪었던 부분은 대략 다음 세 가지이다.

* 그래프의 x 축에 값 할당하기
{% highlight java linenos %}
XAxis xAxis = mLineChart.getXAxis();
xAxis.setValueFormatter(new IAxisValueFormatter() {
      @Override
      public String getFormattedValue(float value, AxisBase axis) {
          if (dateArrayList.size() > (int) value) {
              return dateArrayList.get((int) value);
          } else return null;
      }

      @Override
      public int getDecimalDigits() {
          return 0;
      }
});
{% endhighlight %}
1: LineChart 타입의 mLineChart에서 getXAxis 를 사용한 후, xAxis에 저장한다.<br>
2 ~ 14: 함수를 오버라이드하여 원하는 x 값을 size만큼 출력한다. 이때 6번째 라인에서 리턴하는 dateArrayList의 타입은 ArrayList<String> 이며, value는 dateArrayList의 인덱스를 의미한다. value 값을 int로 type casting 하지 않을 경우 컴파일 에러가 난다.

* x, y 축의 값과 그래프 축 사이에 공간 두기
: 디폴트로 설정된 x, y 축의 값과 그래프 축의 사이 공간이 너무 가까워서 보기 불편한 점이 있었다.

![좁은 공간 예제](/images/2017-09-17-유용한-안드로이드-오픈소스-라이브러리:-MPAndroidChart/little_space.png)

이 문제는 아래의 코드를 삽입하여 해결할 수 있다.

{% highlight java %}
mLineChart.getXAxis().setYOffset(15f);
mLineChart.getAxisLeft().setXOffset(15f);
{% endhighlight %}

* x 축에 값이 중복되어 출력되는 현상 제거하기
: x축에 할당된 값이 6개 이하일 경우 x축에 나타나는 값이 사진과 같이 중복되는 현상이 발생한다.

![중복 예제](/images/2017-09-17-유용한-안드로이드-오픈소스-라이브러리:-MPAndroidChart/overlap.png)

이 문제는 아래의 코드를 삽입하여 해결할 수 있다.

{% highlight java %}
xAxis.setGranularityEnabled(true);
{% endhighlight %}
