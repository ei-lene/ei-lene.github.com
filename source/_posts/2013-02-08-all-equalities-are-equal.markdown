---
layout: post
title: "All equalities are equal..."
date: 2013-02-08 22:02
comments: true
categories: ruby
---

<h3><em>...but some are more equal than others</em></h3>

I started on this trail because I'm a pedant about spelling, and got stuck on <a href="http://rubymonk.com/learning/books/1/chapters/8-control-structures/lessons/43-boolean-expressions-in-ruby">Ruby Monk's first Boolean exercise</a>.

Why was the following solution invalid >> ```name.equal?("Bob")``` <br>
and this solution valid instead? >> ```name.eql?("Bob")```

This set me off researching Ruby equality operators.<br>
There seems to be four types of equality operators:-<br>
<a href="#==">'=='</a><br>
<a href="#eql">'.eql?'</a><br>
<a href="#equal">'.equal?'</a><br>
<a href="#===">'==='</a>

And so it would seem, to misquote George Orwell, that some equalities are more equal than others.

<!--more-->

<a name="=="><strong>==</strong></a><br>
<strong>Simple (or Value) equality.</strong><br>
<em>```==``` returns true if both sides of the operator have the same value, regardless of datatype.</em>

Thus:<br>
<img src="http://ei-lene.github.com/images/2013_02_08/ValueEquality1.png">

However, the Ruby ```==``` operator is not quite as forgiving its Javascript counterpart. It does not convert strings to integers, nor does it recognise upper and lower case versions of the same letter as being of the same value.

Therefore:<br>
<img src="http://ei-lene.github.com/images/2013_02_08/ValueEquality2.png">

<a name="eql"><strong>.eql?</strong></a><br>
<strong>Value and Type Equality</strong><br>
<em>```.eql?``` has stricter equality criteria than ```==```. It tests for equality of value as well as data type.</em>

Hence:<br>
<img src="http://ei-lene.github.com/images/2013_02_08/ValueTypeEquality.png">

<a name="equal"><strong>.equal?</strong></a><br>
<strong>Strict (True or Object) Equality</strong><br>
<em>```.equal?``` has the strictest test of equality*, and is "the most equal of them all". It tests for equality of object ID.</em>

As a newbie Rubyist at <a href="http://flatironschool.com/">the Flatiron School</a>, I have very recently learnt that all strings (even if identical) have their own individual object ID each time they are written (called?), whereas symbols have a unique and immutable object ID, which will remain each time the symbol is called, even if the value has been reassigned. 

The moral of the story was that we should only use symbols (and never strings) as keys in hashes, since they are 'cheaper' than strings in terms of memory and performance (in fact, in researching this, I have discovered that object IDs would be another interesting post for another day, but I digress...)
 
<a href="http://french.about.com/od/vocabulary/a/revenonsanosmoutons.htm">Revenons à nos moutons</a>\*\* - it would seem that this equality test would be passed in fairly limited cases - primarily when symbols or integers are tested against themselves***:<br>
<img src="http://ei-lene.github.com/images/2013_02_08/ObjectEquality.png">


<a name="==="><strong>===</strong></a><br>
<strong>Case Equality (or "Subset equality" or "False Equality")</strong>

Okay, so I've completely made up the equality names in the parenthesis for this. For that matter, all the equality names for all the other operators are also kind of 'made-up' by me, but in those cases, I think they are fairly representative of what those equality operators test for.

<em>```===``` is generally known everywhere, including <a href="http://ruby-doc.org/core-1.9.3/Object.html#method-i-3D-3D-3D">in the Ruby documentation</a>, as "Case equality".</em>

I thought that <a href="http://techbot.me/2011/05/ruby-basics-equality-operators-ruby/">Techbot</a> and <a href="http://www.tutorialspoint.com/ruby/ruby_operators.htm
">TutorialsPoint</a> gave the best illustrations of the Case Equality usage of ```===```, which I will illustrate briefly below:<br>
<img src="http://ei-lene.github.com/images/2013_02_08/SubsetCaseEquality1.png">

While I agree with the above usage (it clearly works!), I see things more simplistically. To me, the ```===``` operator seems to test if the item on right hand side of the operator is a subset of the left hand side of the operator.

For example: <br>
<img src="http://ei-lene.github.com/images/2013_02_08/SubsetCaseEquality2.png">


The ```===``` operator doesn't seem to test for subset in the cases of strings, arrays and hashes, but it works with Regex:<br>
<img src="http://ei-lene.github.com/images/2013_02_08/SubsetCaseEquality3.png">

Anyway, because I think ```===``` is testing if one element (object?) is a subset of another, I don't think that it can be considered an equality test at all, hence my nickname of "False Equality" for it.

<a href="http://stackoverflow.com/users/23649/jtbandes">jtbandes</a> says on <a href="stackoverflow.com">StackOverflow</a> that ```===``` is incredibly useful for implementations in range, regex and proc (and people seem to compare it to the #== operator), but since all that is WAY over my head, I'll just take his word for it, and you can <a href="http://stackoverflow.com/questions/7156955/whats-the-difference-between-equal-eql-and">check it out there</a>.

After all this research, I'm kind of at peace with Ruby's misspelling of ```.eql?```, because I can see that it is necessary to differentiate it from ```.equal?```

But I'm still annoyed with ```elsif```… >:(


<p style="font-size: 70%; font-weight: normal">
Notes:<br>
* In my rather-uninformed opinion (as I'm an absolute newbie), I am more than happy to be enlightened if I've made any mistakes in my blog post.<br>
** A French expression for - to get back to the subject. <a href="http://french.about.com/od/vocabulary/a/revenonsanosmoutons.htm">Check out Laura Lawless's guide to French expressions</a>.<br>
*** I'm finding Object IDs pretty fascinating as I've called .object_id on multiple permutations of data types as part of the research for this blog post. I might write about this piece of geekery fascination another day. Also happy to be enlightened/ corrected if I've made any mistakes.

<p style="font-size: 70%; font-weight: normal">
Resources:<br>
<a href="http://truffles.me.uk/rubys-equality-operator">http://truffles.me.uk/rubys-equality-operator</a><br>
<a href="http://techbot.me/2011/05/ruby-basics-equality-operators-ruby/">http://techbot.me/2011/05/ruby-basics-equality-operators-ruby/</a><br>
<a href="http://www.tutorialspoint.com/ruby/ruby_operators.htm">http://www.tutorialspoint.com/ruby/ruby_operators.htm</a><br>
<a href="http://ruby-doc.org/core-1.9.3/Object.html"> http://ruby-doc.org/core-1.9.3/Object.html</a><br>
<a href="http://stackoverflow.com/questions/7156955/whats-the-difference-between-equal-eql-and"> http://stackoverflow.com/questions/7156955/whats-the-difference-between-equal-eql-and</a>

