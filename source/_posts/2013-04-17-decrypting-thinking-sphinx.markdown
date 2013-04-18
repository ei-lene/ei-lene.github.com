---
layout: post
title: "Decrypting Thinking Sphinx"
date: 2013-04-17 22:47
comments: true
categories: _rails_, thinking-sphinx
---
<h3><em>3 things to be aware of when installing Thinking Sphinx</em></h3>
<img src="http://ei-lene.github.com/images/2013_04_17/mystery_of_the_sphinx.png"><br>

My key takeaway from installing the Thinking Sphinx gem for a project, is that Thinking Sphinx is extremely finickety.

The steps seem simple enough on the <a href="http://pat.github.io/thinking-sphinx/">Thinking Sphinx documentation website</a>, but many errors and hours later, I can attest that it is full of pitfalls for the unwary, or the plain unlucky.

Our initial steps to install Thinking Sphinx for Rails 3.1 and above were to do the following:<br>
1)  brew install sphinx<br>
2)  brew install mysql<br>
3)  added the following to the Gemfile<br>
- gem ‘mysql2’<br>
- gem ‘thinking-sphinx’ 

<strong>Things I learnt from my experience are as follows:</strong>

<strong><em>1)  You need to have a specific (older) version of MySQL installed (5.5.28)</em></strong>

Initially Sphinx threw its toys out of the pram because it couldn’t find the correct version of MySQL because brew had automatically installed the latest version of MySQL (5.6.10)

We downloaded 5.5.30 from <a href="http://dev.mysql.com/downloads/mysql/5.5.html - downloads">dev.mysql.com</a>, which ushered in the next set of fairly obscure errors.

<strong><em>2)  When using Thinking Sphinx with a Postgres database</em></strong> (which we are doing for our project)<strong><em>, install Sphinx with both the mysql and postgres flags</em></strong>


The next set of errors from our attempt to debug and install TS, were mostly variants of: ```‘ERROR: source ‘location_core_0’:  unknown type ‘mysql’ ; skipping’```, ```‘Can't connect to MySQL server [or client or lib]’```

Although <a href="http://pat.github.io/thinking-sphinx/installing_sphinx/mac.html">the docs</a> say that Sphinx will automatically detect whether you have MySQL of PostgresSQL, that wasn’t the case for us.

And the only combination that worked was ```brew install sphinx –mysql –pgsql``` (we had tried all the other permutations before this one finally worked, sort of).

<strong><em>3)  Follow the gem version to the letter</em></strong>

After the installation of sphinx, bundling of gems, and running of ```rake ts:index``` and ```rake ts:start```, we ran our search in rails console, which returned a Thinking Sphinx object ```#<ThinkingSphinx::Search:0x3fc3d2550958>```, which resulted in the error 
```undefined method `next_result' for #<Mysql2::Client:0xac86a54>```
 

We eventually found the solution in <a href="https://github.com/pat/thinking-sphinx/issues/446">Thinking Sphinx's github issues</a>. Our mistake had been to assume that Thinking Sphinx would work with the latest releases of mysql and thinking-sphinx gems, which is why we had not included the specific gem version in our Gemfile. 

But Thinking Sphinx was pernickety enough to require the exact version '0.3.12b5' of ‘mysql2’ (yes, down to ‘b5’!).
 

  
<p style="font-size: 70%; font-weight: normal">
Resources: <br>
http://pat.github.io/thinking-sphinx/<br>
https://github.com/pat/thinking-sphinx
</p>
