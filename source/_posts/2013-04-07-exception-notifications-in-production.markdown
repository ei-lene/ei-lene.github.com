---
layout: post
title: "Exception notifications in production"
date: 2013-04-07 11:46
comments: true
categories: _rails_ exception_notification gems
---

<img src="http://ei-lene.github.com/images/2013_04_07/error_comic.png">
<p style="font-size: 70%; font-weight: normal">
Source: <a href="http://xkcd.com/1024/">XKCD</a>
</p>

The last couple of weeks at Flatiron School, we have been split into groups to work on group projects. <a href="http://anthonywijnen.com/">Anthony</a>, <a href="http://eewang.github.io">Eugene</a>, <a href="http://janeeats.github.io/">Jane</a>, and I have been working on <a href="http://handrai.se/">Handrai.se</a>, an in-class question-workflow-management app. In the process of deploying our group's app, we've come to realise that what works perfectly in development, doesn't always work quite so smoothly in production, because real-life users don't behave the way we assume they do in our seed file and development testing.

And unlike in development, in production, Rails errors are completely cryptic and uninformative:<br>
<img src="http://ei-lene.github.com/images/2013_04_07/production_error.png">

To get a more informative error message, you would need to log into the server, and into the 'current' folder and type in: ```tail -f log/production.log```

But what if you don't personally encounter the error?

<a href="https://twitter.com/aviflombaum">Avi</a> suggested using an 'Exception Notifier' gem to get automatically notified each time an error is triggered in production.
<!--more-->
<h4>Exception Notification 123s:</h4>
<strong><em>1) Read the updated documentation at <a href="https://github.com/smartinez87/exception_notification">https://github.com/smartinez87/exception_notification</a>:</em></strong><br>
Initially, I was really struggling to work with the Exception Notification gem, because I was reading the old, but SEO-optimised documentation at <a href="https://github.com/rails/exception_notification">https://github.com/rails/exception_notification</a>. 

I really should have realised that such a popular gem (#2 on <a href="https://www.ruby-toolbox.com/categories/exception_notification#exception_notification">Ruby-Toolbox</a> with with > 470k downloads) couldn't really have its last commit dated 3 years ago.

It was Blake, who pointed out that there was a newer, and much more active, repo:<br>
<img src="http://ei-lene.github.com/images/2013_04_07/ruby_toolbox.png">
This repo had much clearer instructions.

<strong><em>2) Configuring ActionMailer:</em></strong><br>
Exception notification's instructions assumes that you are using <a href="https://github.com/smartinez87/exception_notification">sendmail</a>. We decided to go with <a href="http://mailgun.com/">mailgun</a>.

You can put the following code in the ```config/environments/production.rb``` and/or ```development.rb``` files depending on whether you want notifications to be sent out only in production or also in development:<br>
{% codeblock lang:ruby %}
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = {
    :authentication => :plain,
    :address => "smtp.mailgun.org",
    :port => 587,
    :domain => "handraise.mailgun.org",
    :user_name => ENV['MAILGUN_LOGIN'],
    :password => ENV['MAILGUN_PASSWORD'],
  }

  config.action_mailer.perform_deliveries = true
  config.action_mailer.raise_delivery_errors = true
{% endcodeblock %}

<strong><em>3) Protecting your API keys, settings and passwords: </em></strong><br>
To protect the security of your passwords etc., you can either use <a href="https://github.com/laserlemon/figaro">the Figaro gem</a>, or do it yourself:<br>
i) Create a ```.yml``` file in the ```config``` folder, e.g. ```application.yml```<br>
ii) Create global variables in the file, but using a colon (:) instead of an equal sign (=), e.g.:
{% codeblock lang:ruby %}
MAILGUN_LOGIN    : postmaster@handraise.mailgun.org
MAILGUN_PASSWORD : [password] 
{% endcodeblock %}
iii) Call these variables using ```ENV['']```, as in the codeblock in step #2 above<br>
iv) Put the ```.yml``` file in your ```.gitignore``` file:
{% codeblock lang:ruby %}
# Ignore application configuration
/config/application.yml
{% endcodeblock %}

<strong><em>Debugging with Exception Notifier</em></strong><br>
Most of Exception Notifier's error messages were detailed and informative. But one in particular (and our first error in fact!) stumped us:<br>
<img src="http://ei-lene.github.com/images/2013_04_07/exception_notification.png"><br>
What isn't precompiled? This is as cryptic an error message as "We're sorry but something went wrong". We initially thought it was an asset precompilation issue, but that wasn't the case.

<a href="https://twitter.com/withloudhands">Bob</a> understood the error immediately. It was "nil isn't precompiled", and he knew this because "isn't precompiled" was slightly further indented than the rest of the message, which indicated a blank space, and hence, "nil". Riiiiggght.
<img src="http://ei-lene.github.com/images/2013_04_07/indented_error_message.png">

Apparently, this error arose because we forgot to include a conditionality in our views, to not render a user photo if a user didn't have a photo. So this was quite easily fixed once we realised what the error was.


<p style="font-size: 70%; font-weight: normal">
Resources:<br>
1) <a href="http://smartinez87.github.io/exception_notification/">Exception Notification by Sebastian Martinez</a><br>
2) SMTP configuration at my classmate Victoria's <a href="http://vicfriedman.github.io/blog/2013/03/24/action-mailer-configurations/">blog </a>
</p>



