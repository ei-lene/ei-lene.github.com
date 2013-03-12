---
layout: post
title: "My top 4 Rails tricks in my first rails app"
date: 2013-03-06 21:18
comments: true
categories: rails
---
<h3><em>Make it work, make it right, …</em></h3>

We began learning Rails last week at the <a href="http://flatironschool.com/">Flatiron School</a>, with lectures and a couple of guided Rails labs / tutorials. 

In this post, I will outline the key development stages* of my first ever non-guided , albeit simple, Rails app (a ToDoList app) and my top tricks & workarounds for the current version of the app.

<strong> TL;DR:</strong><br>
Top 4 tricks: <a href="#f.select">f.select</a> ; <a href="#f.hidden_field">f.hidden_field</a>, <a href="#to_param">to_param</a>, <a href="#button_to">button_to</a>

<strong><a href="http://en.wikipedia.org/wiki/The_Full_Monty">The Full Monty...</a></strong><br>
<!--more-->
As I’m sure most fledgling coders will sympathise with, it seems to be that the biggest hurdle when trying to programme an app (with limited or no guidance) is the ‘coder's block’. The coder’s version of <a href="http://en.wikipedia.org/wiki/Watership_Down">going tharn</a>  at the sight of a blank text-editor. What seemed doable (not to say easy) enough in a guided tutorial, suddenly seems impossible – where do I begin? what do I do?

After several weeks of countless panic attacks as part of my course, I am slowly beginning to learn to deal with my coder’s block, through the following mantras:<br>
1)  breathe… <a href="http://en.wikipedia.org/wiki/Keep_Calm_and_Carry_On">Keep Calm and Carry On</a><br>
2)  <a href="http://c2.com/cgi/wiki?MakeItWorkMakeItRightMakeItFast">Make It Work, Make It Right, Make It Fast </a>

On the second mantra, my key takeaway was that even great programmers recognise that Rome wasn’t build in a day, so for me, for now, I just need to Make It Work, <em>by any means necessary</em>. Even if I have use brute force** to machete my way through, the first step is to get it working.

<strong>.v001</strong><br>
<strong><em>Making it work:</em></strong><br>
With that in mind, the first major iteration of my ToDoList app was minimalistic to the extreme***. My only aims were: <br>
1)  To create lists and show them on the home page<br>
2)  To add tasks to lists<br>
<img src="http://ei-lene.github.com/images/2013_03_06/v1_home.png"><br>
<img src="http://ei-lene.github.com/images/2013_03_06/v1_addtasks.png">

<strong><em>Key issues / next steps:</em></strong><br>
1)  The unordered list was clearly a bad idea – users would have to guess and/or remember the number of the list they want to add their task to<br>
2)  The lists should be ordered and should be displayed on the ‘Add tasks’ page<br>

<strong>.v002</strong><br>
<strong><em>Making it right:</em></strong><br>
<a name="f.select"><em>Top Trick #1 – ```f.select```</em></a><br>
When discussing the lab in class, I mentioned that I was intending to add radio buttons to the Task ```new.html.erb``` page so users can choose the list to add their task to.
<a href="https://twitter.com/aviflombaum">Avi</a> suggested using a drop-down menu instead, and showed briefly walked through the ```f.select``` option in the Rails ```form_for``` helper

Thus, with a short line of code, I significantly improved my user experience and interface:
``` ruby new.html.erb
<%= f.select :list_id, List.all.collect { |l| [l.name, l.id]} %>
```
<img src="http://ei-lene.github.com/images/2013_03_06/v2_addtasks.png">

<strong><em>Making it work:</em></strong><br>
1) Users should be able to add tasks to a specific list when on that list page<br>
<a name="f.hidden_field"><em>Top Trick #2 – ```f.hidden_field``` </em></a><br>
My initial intention was to have an “Add task to this list” link which would redirect to a new route along the lines of ```/lists/:id/tasks/new```.

However, despite my valiant efforts at throwing all sorts of different code permutations and combinations at Rails, the standard ```new_task_path``` could not seem to register the fact that it had just been routed from a specific list id, and store that list id.****

As a workaround, I decided to implement a task form on the list show page (```show.html.erb```) with ```f.hidden_field(:list_id)```, a technique that I learnt in <a href="http://tutorials.jumpstartlab.com/projects/blogger.html">Jumpstart Lab’s blogger tutorial</a> that we were assigned. 

And having implemented that, I’ve realised that it works really well, and is more optimal than having to click on a link to an alternate path. Because in this case, each time the user adds a new task to a list, the list page reloads, automatically allowing the user to add another new task to the list.<br>
<img src="http://ei-lene.github.com/images/2013_03_06/v2_addtasktospecificlist.png">

3) Users should be able to delete and edit lists. As an initial testing step, I implemented the ability for users to:<br>
i) delete the entire list only<br>
ii) update list name and existing task descriptions<br>
 <img src="http://ei-lene.github.com/images/2013_03_06/v2_editlist.png">

<strong><em>Key issues / next steps:</em></strong><br>
1)  Change the routes of list from id (list/:id) to name (list/:name), to make the routes look more elegant<br>
2)  Flesh out edit list functionality – users should be able to delete individual tasks in a specific list<br>

<strong>.v003</strong><br>
<strong><em>Making it right:</em></strong><br>
1) Added list routing via ```permalink``` (where ```permalink = name.downcase.gsub(“ “,”-“```)<br>
<a name="to_param"><em>Top Trick #3 – ```to_param```</em></a><br>
<a href="https://twitter.com/aviflombaum">Avi</a> mentioned in class that you could edit the url routing by editing the to_param method in the relevant model.

I must’ve missed out the second part of his words, because I implemented that, then proceeded to struggle as Rails spat out variants of the following error as I diced and spliced my code creatively to try to get it working:<br>
<img src="http://ei-lene.github.com/images/2013_03_06/error_message.png">

After extensive googling, I found my solution at <a href="http://railscasts.com/episodes/63-model-name-in-url">Railscasts</a>.

I initially used the first method,  implementing ```"#{id}-#{permalink}"``` in ```to_param``` and retaining the ```.find(params[:id])``` in the Controller. But the id of the lists would show up in the url which would not correspond to their position based on the number of existing lists (because deleted lists would retain their unique primary_key id), which I found particularly inelegant.

And so I opted for the second method instead:
```ruby list.rb
def to_param
  permalink
end
```
``` ruby lists_controller.rb
def edit
    @list = List.find_by_permalink(params[:id])
end
```
<img src="http://ei-lene.github.com/images/2013_03_06/v3_addtasks.png">

<strong><em>Making it work:</em></strong><br>
Added functionality to allow users to delete individual tasks in a specific list, even if the ‘Delete this task’ link was very ugly and incongruous next to the compact ‘Update Task’ submit button.<br>
<img src="http://ei-lene.github.com/images/2013_03_06/v3_editlisttasks.png">

<strong><em>Key issues / next steps:</em></strong><br>
1) Change the styling of ‘Delete this task’<br>
2) Improve overall styling

<strong>.v004</strong><br>
<strong><em>Making it right:</em></strong><br>
1)  Overall styling improved<br>
<img src="http://ei-lene.github.com/images/2013_03_06/current_list.png">

2) Button generated for ‘Delete Task’ <br>
<a name="button_to"><em>Top Trick #3 – ```button_to```</em></a><br>
Before I discovered the delightful ```button_to``` option via googling, I thought the only way to generate buttons in rails was to create a ```f.submit``` within the ```form_for``` helper. And so I tried various ways to implement a ```method: :delete``` and routing to ```task_path(t)``` within the ```f.submit```.

Eventually, I gave up banging my head on the ```f.submit``` wall and tried a different tack - CSS buttons! But I found CSS buttons very unwieldy and impossible to style to match the ‘Update Task’ button. 

```button_to``` was a life-saver. With one line of elegant code, I managed to generate a lovely Rails-format button:<br>
```<%= button_to 'Delete Task', task_path(t), method: :delete %>```
<img src="http://ei-lene.github.com/images/2013_03_06/current_editlist.png">

<strong><em>Conclusion / Next steps:</em></strong><br>
Even though my ToDoList app is still very basic for now, it has come a long way from the start, as this post has demonstrated. In fact, if I had tried to tackle even all of the basic functionality and UI in the current version of the app, I wouldn’t have dared to begin the project.

Instead, I put on my blinkers and took one baby step at a time. As my classmate <a href="https://twitter.com/janeeats">Jane</a> so aptly puts it: <a href="http://janeeats.github.com/blog/2013/02/24/how-to-eat-an-elephant/">“How Do You Eat an Elephant? One Bite at a Time.”</a>

So far, I have only been iterating through the process of making it work and making it right, as I’m not yet at the stage where I have the skills (or the project complexity) to make it fast.

<strong><em>Next steps:</em></strong><br>
1) Change the navigation links so that they are conditional – e.g. links to Home don’t show up when a user is already on the home page<br>
2) Add authorisation for Users to create, edit and delete lists<br>

Note: Github’s rollback has been invaluable in the creation of this post, namely the commands: ```git reset –-hard <commit sha id>``` & ```git push -f```

<p style="font-size: 70%; font-weight: normal">
* In reality, there were a lot of mini-steps / stages. But in the interest of simplicity, and because I didn’t commit often enough to be able to rollback commits to each mini stage, I am grouping the changes into 4 stages<br>
** The phrase "brute force" is attributable to my classmate <a href="http://rseshan.github.com/">Rahul</a><br>
*** With bare bones styling, because the white background and default serif fonts give me a headache<br>
**** I spoke to <a href="https://twitter.com/withloudhands">Bob</a> about this on Monday, and he told me that this would needed to be done via nested routes, a topic which will covered in future<br>
</p>
