---
layout: post
title: Collecting data on form submission
description: Collect subscriber data on form submission...
date: 2024-06-03 15:01:35 +0300
image: '/images/03.jpg'
tags: [Ampscript, CloudPage]
---

This sample pulls in data from an email into CloudPages inorder to capture a subscribers polling results (@Poll_Submission). This snippet can be combined with <a href="/create-a-success-page-on-form-submission">Creating a success page</a>.

{% highlight js %}
%%[
SET @SubscriberKey = RequestParameter("SubscriberKey")
SET @Email_Address = RequestParameter("Email_Address")
SET @Poll_Submission = RequestParameter("Poll_Submission")


SET @DE_Lookup = 'Poll_submission_de_test'
set @hasSubmitted = lookUp(@DE_Lookup, 'Poll_Submission', 'SubscriberKey', @SubscriberKey)

IF NOT EMPTY(@hasSubmitted) THEN 
    /* Display success message */
ELSE
    /* If the user doesn't exist in @DE_Lookup, use InsertDE() to enter them into the Data Extension */
    InsertDE(@DE_Lookup,
        'SubscriberKey', @SubscriberKey,
        'Email_Address', @Email_Address,
        'Poll_Submission', @Poll_Submission,
        'Date_Modified', formatDate(now(), 'l', 'h:mm tt')
    )
ENDIF
]%%
{% endhighlight %}