---
layout: post
title: "자바에서 자주 쓰는 String, Array 관련 메소드"
author: "Hyemin"
---


### other type to String
{% highlight java linenos %}
String str = String.valueOf(boolean value);// true or false
str = String.valueOf(int value);
str = String.valueOf(long value);
str = String.valueOf(float value);
str = String.valueOf(double value);
str = String.valueOf(char value);
str = String.valueOf(char[] values);// char array to String
str = String.valueOf(char[] values, int startIndex, int count);// get String from startIndex to startIndex+count-1 index
str = String.valueOf(Object object);
{% endhighlight %}
[관련 글: Integer.toString(int i)? String.valueOf(int i)?](https://stackoverflow.com/questions/3335685/integer-tostring)

### String to other type
{% highlight java linenos %}
int integerValue = Integer.parseInt(String str);
float floatValue = Float.parseFloat(String str);
double doubleValue = Double.parseDouble(String str);
byte byteValue = Byte.parseByte(String str);
short shortValue = Short.parseShort(String str);
long longValue = Long.parseLong(String str);
{% endhighlight %}

### String to Character Array
{% highlight java linenos %}
String someString= "Hello";
char[] someCharacterArray = someString.toCharArray();
//someCharacterArray에 저장되는 값은 {'H', 'e', 'l', 'l', 'o'}
{% endhighlight %}

### get substring, character, index from String
{% highlight java linenos %}
String str = "012345";
str.substring(2);// "2345"
str.substring(0, 2);// "01"
String text = "abcdef"
str.charAt(0);// 'a'
str.indexAt('a');// 0
{% endhighlight %}

### Sort
{% highlight java linenos %}
int[] array = {2, 3, 1, 4};
Arrays.sort(array);// {1, 2, 3, 4}
Arrays.sort(array, Collections.reverseOrder()); // {4, 3, 2, 1}

ArrayList<Integer> list = Arrays.asList(2, 3, 1, 4);
Collections.sort(list);// [1, 2, 3, 4]
Collections.reverse(list);// [4, 3, 2, 1]
{% endhighlight %}
