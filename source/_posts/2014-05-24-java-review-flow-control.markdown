---
layout: post
title:  "Java Review - Flow Control"
date:   2014-05-24
categories: java
---
#Selection
##If statements with optional else clauses
{% highlight java %}
if (boolean condition)
  statement;
else
statement;
{% endhighlight %}
##Switch statements
{% highlight java %}
switch (interger value) {
  case FIRST_NAME:
    statements;
  case SECOND_NAME:
    statements;
  default:
    statements;
}
{% endhighlight %}
<!-- more -->
#Repetition

##While
{% highlight java %}
while (scan.hasNext()) {
  statements; // repeated until false above
}
{% endhighlight %}

##For
{% highlight java %}
for (int i = 0; i < MAX; i++) {
  statements; // repeated until false above
}
{% endhighlight %}

##Do ... while
{% highlight java %}
do {
  statements; // repeated until false above
} while (done!);
{% endhighlight %}