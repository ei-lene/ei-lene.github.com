---
layout: post
title: "Front-end form validation - my top 3 JQuery APIs"
date: 2013-03-11 22:15
comments: true
categories: _javascript_ _jquery_ validation .submit .blur .keyup
---
Last week, we had <a href="http://jonathangrover.com/">Jonathan Grover</a> guest lecture us for a week on Javascript and JQuery.

It was a really interesting week during which we learnt how useful Javascript is in improving the user interface and experience, how JQuery resolves issues around Javascript cross-browser compatibility, and applied Javascript and JQuery in many contexts, including interacting with Twitter, Flickr and Google Maps APIs.

One of the practical applications of Javascript / JQuery was in form validations, which we combined with back-end Rails validation. While back-end validation is used to ensure data integrity, front-end validation is used to provide end-users with timely feedback to create a more positive user-experience.

<!--more-->

In designing a simple user sign-up form (as shown at the bottom of this post), my top 3 JQuery APIs to provide appropriate and timely user feedback are the following:<br>
<a href="#submit">.submit()</a><br>
<a href="#blur">.blur()</a><br>
<a href="#keyup">.keyup()</a><br>

<a name="submit"><strong>.submit()</strong></a><br>
```.submit()``` provides users with feedback upon submission of the form. Be sure to use ```&``` instead of ```&&``` so that users get all the error messages at once. – There is nothing more annoying than to get drip-fed error messages each time you click ‘submit’.

{% codeblock .submit() lang:js %}
// other code truncated (including definition of functions)
  newUserForm.submit(function(){
    if(validateUsername() & validateFirstname() & validateLastname() & validateEmail() & validatePassword()) {
      return true;
    } 
    else {
      return false;
    }
  }); 
{% endcodeblock %}

<a name="blur"><strong>.blur()</strong></a><br>
I would argue that giving users feedback upon submission is too late, because it requires the user to go back to the top and reconsider every input field again, which requires more mental effort than if feedback was given at each input field, before the brain is occupied with considering a new topic

To this end, ```.blur()``` is very useful. ```.blur()``` is triggered when the user leaves a particular form input field, that is, as soon as it appears that a user is ‘done’ with that field.

{% codeblock .blur() lang:js %}
// other code truncated (including definition of functions)
  userName.blur(function(){
    validateUsername(); 
  });

  firstName.blur(function(){
    validateFirstname(); 
  });

  lastName.blur(function(){
    validateLastname(); 
  });

  email.blur(function(){
    validateEmail(); 
  });

  password.blur(function(){
    validatePassword(); 
  });
{% endcodeblock %}

In my code, even though I added ```.blur()``` validation for each input field, I also kept my ```.submit()``` validation to prevent the form from being submitted unless all conditions were met.

<a name="keyup"><strong>.keyup()</strong></a><br>
This provides even more immediate feedback than ```.blur()```, as the feedback can be provided ‘live’ as each key is pressed and released.
Real-time feedback may not be appropriate for every single field. For example, it might be annoying to get a live commentary as you are trying to pick a username. 

However, it can be used to great effect for specific cases. For example, it is mostly commonly used to give real-time feedback on password strength as users are choosing their password.

In my form, I chose to use ```.keyup()``` during password choice (to indicate password strength, even though I am not requiring a ‘strong’ password), as well as for the validation of confirmation emails and passwords.

{% codeblock .keyup() lang:js %}
// other code truncated (including definition of functions)
  emailInputs.keyup(function(){
    emailConfVal = emailConfirmation.val();
    emailVal = email.val();
    emailConfError.show(); 
    if(emailVal===''||emailConfVal==='') {
        emailConfError.hide();
    }
    else if(emailVal === emailConfVal){
      emailConfError.text("Email entries match").addClass('green');
    }
    else {
      emailConfError.hide();
      emailConfError.text("Confirmation email does not match email").addClass('red');
      emailConfError.show();
    }
  });
{% endcodeblock %}

Finally, I have also used ```.text()``` and ```.addClass()``` extensively.

{% codeblock .text() & .addClass() lang:js %}
// example usage
function validatePasswordStrength(){
    var lowercase = /[a-z]/
    var uppercase = /[A-Z]/
    var numeric = /[0-9]/
    var passwordVal = password.val();
    passwordError.show(); 
    if(lowercase.test(passwordVal) && uppercase.test(passwordVal) && numeric.test(passwordVal)){
      passwordError.text("Password strength: Strong").removeClass().addClass('green');
    }
    else if(lowercase.test(passwordVal) && uppercase.test(passwordVal)){
      passwordError.text("Password strength: Medium").removeClass().addClass('amber');
    }
    else if(numeric.test(passwordVal) && uppercase.test(passwordVal)){
      passwordError.text("Password strength: Medium").removeClass().addClass('amber');
    }
    else if(numeric.test(passwordVal) && lowercase.test(passwordVal)){
      passwordError.text("Password strength: Medium").removeClass().addClass('amber');
    }
    else {
      passwordError.text("Password strength: Weak").addClass('red');
    }
  }
{% endcodeblock %}

I used ```.text()``` to dynamically change the feedback message to the user, indicating whether a password is “weak”, “medium” or “strong”.

I also used ```.addClass()``` to give visual feedback to users though changing the styling of feedback messages (in my case, toggling between ‘green’, ‘amber’ and ‘red’ traffic light colors).

Resulting form with errors displayed: <br>
<img src="http://ei-lene.github.com/images/2013_03_11/displaymessages.png"><br>

<p style="font-size: 70%; font-weight: normal">
My github code for this form: <a href="https://github.com/ei-lene/validation_app">https://github.com/ei-lene/validation_app</a><br>
Two great resources: <br>
<a href="http://api.jquery.com/"> api.jquery.com</a><br>
<a href="http://oscarotero.com/jquery/">oscar otero</a><br>
</p>

