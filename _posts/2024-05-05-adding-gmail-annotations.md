---
layout: post
title: Adding Gmail Annotations
description: Show off your promo code or discount by adding annotations to Gmail... 
date: 2024-05-05 15:01:35 +0300
image: '/images/14.jpg'
tags: [Email]
---

Basic setup for adding Gmail Annotations. You can also use Google's <a href="https://developers.google.com/gmail/promotab/preview" target="_blank">Annotation Guide</a> to test your code or look up additional samples.

{% highlight markdown %}
<!-- Gmail Discount Offer & Products
     Place the below code right before </head> -->
<div itemscope="" itemtype="http://schema.org/Organization">
    <meta itemprop="name" content="Dominic Belfiori" />
    <meta itemprop="logo" content="https://dominicbelfiori.com/images/portrait-dominic.jpg" />
    <meta itemprop="url" content="https://dominicbelfiori.com/" />
</div>

<div itemscope="" itemtype="http://schema.org/EmailMessage">
    <meta itemprop="subjectLine" content="Please add subject line" />
</div>

<div itemscope itemtype="http://schema.org/DiscountOffer">
    <meta itemprop="description" content="25% OFF" />
    <meta itemprop="discountCode" content="YOURULE25" />
    <meta itemprop="availabilityStarts" content="2024-01-31T00:00:00-0800" />
    <meta itemprop="availabilityEnds" content="4000-12-31T23:59:59-0800" />
</div>
{% endhighlight %}
