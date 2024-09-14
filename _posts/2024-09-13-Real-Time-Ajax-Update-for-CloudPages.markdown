---
layout: post
title: Real-Time AJAX Update for CloudPages
description: display form updates in real-time using AJAX
date:   2024-09-13 15:01:35 +0300
image:  '/images/post_retrieving-data.jpg'
tags: [ampscript, js, cloudpage]
---

<h4>AMPscript in CloudPages Form</h4>

This basic sample is going to require 2 CloudPages. One to host your HTML, one to process the request.

<h4>HTML Page</h4>
{% highlight js %}
<form id="myForm">
    <label for="name">Name:</label>
    <input type="text" id="name" name="name">
    <button type="submit">Submit</button>
</form>
<div id="response"></div>
<div id="error"></div>

<script>
    document.getElementById('myForm').addEventListener('submit', function(event) {
        event.preventDefault(); // Prevent the form from submitting the traditional way

        var xhr = new XMLHttpRequest();
        xhr.open('POST', 'YOUR_CLOUDPAGE_URL', true); // Replace with your CloudPage URL
        xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');

        xhr.onreadystatechange = function() {
            if (xhr.readyState === XMLHttpRequest.DONE) {
                if (xhr.status === 200) {
                    document.getElementById('response').innerHTML = xhr.responseText;
                    document.getElementById('error').innerHTML = ''; // Clear any previous error messages
                } else {
                    document.getElementById('error').innerHTML = 'AJAX Error: ' + xhr.statusText;
                }
            }
        };

        var formData = new FormData(document.getElementById('myForm'));
        var encodedData = new URLSearchParams(formData).toString();
        xhr.send(encodedData);
    });
</script>
{% endhighlight %}

<h4>Server-Side Script (2nd CloudPage)</h4>
{% highlight js %}
%%[
    VAR @name, @response
    SET @name = RequestParameter("name")
    SET @response = CONCAT("Hello, ", @name, "! Your form has been submitted successfully.")
]%%
%%=v(@response)=%%
{% endhighlight %}