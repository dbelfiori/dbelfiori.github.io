---
layout: post
title: Create a Success Page on Form Submission
description: Keep the user on the same page after submitting a form using RequestParameter()... 
date: 2024-06-02 15:01:35 +0300
image: '/images/04.jpg'
tags: [Ampscript, CloudPage]
---

Basic Ampscript to render a success page after clicking the form Submit button.
{% highlight markdown %}
<div class="container">
    <div class="row">
        <div class="col">
            %%[IF RequestParameter("submitted") == 'true' THEN
            /* add in InsertDE() or Entry Event */]%%
            <p>Success Page</p>
            %%[ELSE]%%
            <form action="%%=RequestParameter('PAGEURL')=%%" method="post">
                <div>
                    <label for="firstname">First name:</label>
                    <input type="text" id="firstname" name="firstname">
                </div>
                <div>
                    <label for="lastname">Last name:</label>
                    <input type="text" id="lastname" name="lastname">
                </div>
                    <input name="submitted" type="hidden" value="true">
                    <button class="button" type="submit" value="Submit">Submit</button>
            </form>
            %%[ENDIF]%%
        </div>
    </div>
</div>
{% endhighlight %}