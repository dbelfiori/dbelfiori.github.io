---
layout: post
title: Collecting data on form submission
description: 
date: 2024-03-29 15:01:35 +0300
image: '/images/p_04.jpg'
tags: [Ampscript, Project]
---
{% highlight js %}
%%[
SET @SubscriberKey = RequestParameter("SubscriberKey")
SET @Email_Address = RequestParameter("Email_Address")
SET @Form_Submission = RequestParameter("Form_Submission")


SET @DE_Lookup = 'form_submission_de_test'
set @hasSubmitted = lookUp(@DE_Lookup, 'Form_Submission', 'SubscriberKey', @SubscriberKey)

IF NOT EMPTY(@hasSubmitted) THEN 
    /* Display sucess message */
ELSE
    /* If the user doesn't exist in @DE_Lookup, use InsertDE() to enter them into the Data Extension */
    InsertDE(@DE_Lookup,
        'SubscriberKey', @SubscriberKey,
        'Email_Address', @Email_Address,
        'Form_Submission', @Form_Submission,
        'Date_Modified', formatDate(now(), 'l', 'h:mm tt')
    )
ENDIF
]%%
{% endhighlight %}
{% highlight markdown %}
%%[
SET @SubscriberKey = RequestParameter("SubscriberKey")
SET @Email_Address = RequestParameter("Email_Address")
SET @Form_Submission = RequestParameter("Form_Submission")


SET @DE_Lookup = 'form_submission_de_test'
set @hasSubmitted = lookUp(@DE_Lookup, 'Form_Submission', 'SubscriberKey', @SubscriberKey)

IF NOT EMPTY(@hasSubmitted) THEN 
    /* Display sucess message */
ELSE
    /* If the user doesn't exist in @DE_Lookup, use InsertDE() to enter them into the Data Extension */
    InsertDE(@DE_Lookup,
        'SubscriberKey', @SubscriberKey,
        'Email_Address', @Email_Address,
        'Form_Submission', @Form_Submission,
        'Date_Modified', formatDate(now(), 'l', 'h:mm tt')
    )
ENDIF
]%%
{% endhighlight %}

![]({{site.baseurl}}/images/p_01-1.jpg)