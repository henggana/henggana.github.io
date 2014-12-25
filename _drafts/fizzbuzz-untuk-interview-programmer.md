---
layout: post
title: Fizzbuzz, untuk interview programmer
comments: true
---

### Fizzbuzz

{% highlight ruby %}
def fizzbuzz(n)
  1.upto(n) do | i |
    divided_by_three = (i % 3 == 0)
    divided_by_five = (i % 5 == 0)
    divided_by_fiveteen = divided_by_three && divided_by_five

    if divided_by_three
      puts "fizzbuzz"
    elsif divided_by_five
      puts "fizz"
    elsif divided_by_fiveteen
      puts "buzz"
    else
      puts i
    end
  end
end

fizzbuzz(100)
{% endhighlight %}

Yey!