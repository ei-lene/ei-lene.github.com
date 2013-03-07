---
layout: post
title: "My top 4 Rails tricks in my first rails app"
date: 2013-03-06 21:18
comments: true
categories: rails
---
<h3><em>Make it work, make it right, …</em></h3>

We began learning Rails last week at the Flatiron School, with lectures and a couple of guided Rails labs / tutorials. 

In this post, I will outline the key development stages* of my first ever non-guided , albeit simple, Rails app (a ToDoList app) and my top tricks & workarounds for the current version of the app.

<strong> TL;DR:</strong><br>
Top 4 tricks: <a href="#f.select">f.select</a> ; <a href="#f.hidden_field">f.hidden_field</a>, <a href="#to_param">to_param</a>, <a href="#button_to">button_to</a>

<strong><a href="http://en.wikipedia.org/wiki/The_Full_Monty">The Full Monty...</a></strong><br>
As I’m sure most fledgling coders will sympathise with, it seems to be that the biggest hurdle when trying to programme an app (with limited or no guidance) is the ‘code block’. The coder’s version of <a href="http://en.wikipedia.org/wiki/Watership_Down">going tharn</a>  at the sight of a blank text-editor. What seemed doable (not to say easy) enough in a guided tutorial, suddenly seems impossible – where do I begin? what do I do?

After several weeks of countless panic attacks as part of my course, I am slowly beginning to learn to deal with my coder’s block, by the following mantras:<br>
1)  breathe… <a href="http://en.wikipedia.org/wiki/Keep_Calm_and_Carry_On">Keep Calm and Carry On</a><br>
2)  <a href="http://c2.com/cgi/wiki?MakeItWorkMakeItRightMakeItFast">Make It Work, Make It Right, Make It Fast </a>

On the second mantra, my key takeaway was that even great programmers recognise that Rome wasn’t build in a day, so for me, for now, I just need to Make It Work, <em>by any means necessary</em>. Even if I have use brute force to machete my way through, the first step is to get it working.

<strong>.v001</strong><br>
<em>Making it work:</em><br>
And with that in mind, the first major iteration of my ToDoList app was minimalistic to the extreme*. My only aims were: <br>
1)  To create lists and show them on the home page<br>
2)  To add tasks to lists<br>
<img src="http://ei-lene.github.com/images/2013_03_06/v1_home.png"><br>
<img src="http://ei-lene.github.com/images/2013_03_06/v1_addtasks.png">

<em>Key issues / next steps:</em><br>
1)  The unordered list was clearly a bad idea – users would have to guess and/or remember the number of the list they want to add their task to<br>
2)  The lists should be ordered and should be displayed on the ‘Add tasks’ page<br>

<strong>.v002</strong>
<em>Making It Right:</em><br>
<a name="f.select"><em>Top Trick #1 – ```f.select```</em></a><br>
When discussing the lab in class, I mentioned that I was intending to add radio buttons to the Task ```new.html.erb``` page to allow users to choose the list to add their task to.
Avi suggested using a drop-down menu instead, and showed briefly walked through the ```f.select``` option in the Rails ```form_for``` helper

Thus, with a short line of code: ```<%= f.select :list_id, List.all.collect { |l| [l.name, l.id]} %>```, I significantly improved my user experience and interface:<br>
<img src="http://ei-lene.github.com/images/2013_03_06/v2_addtask.png">