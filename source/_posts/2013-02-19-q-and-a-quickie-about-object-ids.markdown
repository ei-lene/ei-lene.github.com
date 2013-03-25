---
layout: post
title: "Q&amp;A Quickie - about object ids"
date: 2013-02-19 23:49
comments: true
categories: ruby, object_ids
---

I became intrigued about object ids when I was researching into the ```.equal?``` operator in <a href="http://ei-lene.github.com/blog/2013/02/08/all-equalities-are-equal/">my earlier post on Ruby equalities</a>, and also when I was wading through <a href="http://rubykoans.com/">Ruby Koans’ </a>about_object.rb.

And so, here’s my summary on the questions I had, and the answers that my research yielded:<br>
<a href="#q1">1) What is an object id?</a><br>
<a href="#q2">2) Why is true.object_id != 1?</a><br>
<a href="#q3">3) Which objects have fixed object_ids, and what are the practical implications?</a>

<!--more-->
<strong><a name="q1">1) What is an object id?</a></strong><br>
An object id is a unique identifier for an object, much like a fingerprint. All objects have an object id. In Ruby, the method 
```.object_id ``` returns an object’s unique identifier, which is presented by a number or a string of numbers. 

<strong><a name="q2">2) Why is true.object_id != 1?</a></strong><br>
Given that true and false are a fundamental part of most programming languages, I expected the object id for true to be 1, and the object id for false to be 0.

So it surprised me to find that while ``` false.object_id == 0```, ```true.object_id == 2```, and ```nil.object_id == 4```. Why do true and nil have such strange object ids?

The reason for that is that the other, perhaps more obvious numbers (like 1 and 2) are taken up by integers due to the pattern for Fixnum (integer) object ids.

As was tested in Ruby Koans’ about_object.rb, in Ruby, the object ids for integers (i) follow the pattern: ```i * 2 +1```
such that:
<img src="http://ei-lene.github.com/images/2013_02_19/fixnum1.png">

This pattern also holds true for negative integers:
<img src="http://ei-lene.github.com/images/2013_02_19/fixnum2.png">

<strong><a name="q3">3) Which objects have fixed object_ids, and what are the practical implications?</a></strong><br>
As part of my earlier post on Ruby equalities, I tinkered around a lot with ```.object_id``` in irb, researching the application of the ```.equal?``` operator.

Overall, objects seem to fall into 3 categories:<br>
<a href="fixed">i)  those with fixed object ids</a><br>
<a href="#temporary">ii) those with temporarily fixed object ids</a><br>
<a href="#changing">iii)  those with changing object ids</a>

<em><a name="fixed">Objects with Fixed object ids:</a></em><br>
<em>1) Fixnum</em><br>
As mentioned above, integers have fixed object ids.

<em>2) Symbols</em><br>
Once created, symbols have immutable object ids, which will remain constant, even if the values have been reassigned.

For example:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/symbols.png"><br>
In all cases, the object_id of the symbol name is identical

<em><a name="temporary">Objects with temporarily fixed object ids:</a></em><br>
<em>1) Variables </em><br>
Once created, variables will have the same object id each time it is called, unless the variable has been reassigned, in which case, the variable object id will change. While the variable retains its assigned value, its object id is temporarily constant:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/variables1.png"><br>
If a variable is assigned to a Fixnum, the variable will take on that Fixnum’s object id: <br>
<img src="http://ei-lene.github.com/images/2013_02_19/variables2.png"><br>
This will also apply in the case where a variable is assigned to a symbol:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/variables3.png">

<em>2) Arrays</em><br>
Like variables, arrays have a temporarily fixed object id while the contents of the array have not changed: <br>
<img src="http://ei-lene.github.com/images/2013_02_19/arrays.png">


<em>3) Hashes</em><br>
Like variables and arrays, hashes have a temporarily fixed object id while the contents of the array have not changed:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/hashes.png">

<em><a name="changing">Objects with changing object ids:</a></em><br>
1) Floats<br>
Floats have new and different object ids, each time they are called:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/floats.png">

<em>2) Strings</em><br>
Strings have new and different object ids, each time they are called: <br>
<img src="http://ei-lene.github.com/images/2013_02_19/strings.png">

<em>3) Instances of an object</em><br>
A new object, once created, behave very much like variables – they have the same object id each time they are called, as long as they have not been modified. Each iteration of the object will result in a temporarily fixed object id:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/objectinstances1.png">

However, each instance of an object, including clones of another object, have their own unique object id:<br>
<img src="http://ei-lene.github.com/images/2013_02_19/objectinstances2.png">

<em>Implications / applications:</em><br>
As far as I know, the primary (most common) application of the above object id properties, are in the usage of symbols rather than strings for naming things or representing data.

Given that symbols have an immutable object id once they are created, whereas strings have a different and individual object id each time they are called, symbols take up less space in the computer’s memory. Hence, we should use symbols instead of strings as hash keys, or attributes – because if the hash key or attribute will be called over and over again, using symbols will significantly increase the performance of the program.

I found <a href="http://www.robertsosinski.com/2009/01/11/the-difference-between-ruby-symbols-and-strings/">Robert Sosinki’s tutorial on Symbols vs. Strings </a>particularly illuminating.






