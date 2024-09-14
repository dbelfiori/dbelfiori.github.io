---
layout: post
title: Inject a user into a Journey on form submission.
description: A method to inject a user into a journey on form submission, using a Smart Capture form along with the Journey Builder API.
date:   2024-08-17 15:01:35 +0300
image:  '/images/post_journey-implementation.jpg'
tags: [ampscript, ssjs, cloudpage]
---

<h4>AMPscript in CloudPages Form</h4>
{% highlight js %}
%%[
    VAR @firstName, @lastName, @email
    SET @firstName = RequestParameter("firstName")
    SET @lastName = RequestParameter("lastName")
    SET @email = RequestParameter("email")

    /* Insert data into Data Extension */
    InsertDE("YourDataExtension", "FirstName", @firstName, "LastName", @lastName, "Email", @email)
]%%
{% endhighlight %}

<h4>SSJS to Fire the Event</h4>
{% highlight js %}
<script runat="server">
    Platform.Load("Core", "1");

    var firstName = Request.GetFormField("firstName");
    var lastName = Request.GetFormField("lastName");
    var email = Request.GetFormField("email");

    var payload = {
        "ContactKey": email,
        "EventDefinitionKey": "YOUR_EVENT_DEFINITION_KEY",
        "Data": {
            "FirstName": firstName,
            "LastName": lastName,
            "Email": email
        }
    };

    var url = "https://YOUR_SUBDOMAIN.rest.marketingcloudapis.com/interaction/v1/events";
    var contentType = "application/json";
    var headerNames = ["Authorization"];
    var headerValues = ["Bearer YOUR_ACCESS_TOKEN"];

    var result = HTTP.Post(url, contentType, Stringify(payload), headerNames, headerValues);
    Write("Result: " + result.StatusCode);
</script>
{% endhighlight %}