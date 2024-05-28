---
layout: post
title: Find a Data Extension and it’s folder path using SSJS
description: If you work with multiple Data Extensions in different folders, create a CloudPage app to help navigate... 
date: 2024-01-24 15:01:35 +0300
image: '/images/p_01.jpg'
tags: [SSJS, Project]
featured: true
---
{% highlight markdown %}
<!-- Gmail Discount Offer & Products
     Place the below code right before </head> -->
<div itemscope="" itemtype="http://schema.org/Organization">
    <meta itemprop="name" content="Petco" />
    <meta itemprop="logo" content="https://assets.petco.com/petco/image/upload/f_auto,q_auto/Petco_Logo.png" />
    <meta itemprop="url" content="https://www.petco.com/shop/en/petcostore" />
</div>

<div itemscope="" itemtype="http://schema.org/EmailMessage">
    <meta itemprop="subjectLine" content="Happy International Cat Day 😸 25% OFF" />
</div>

<div itemscope itemtype="http://schema.org/DiscountOffer">
    <meta itemprop="description" content="25% OFF" />
    <meta itemprop="discountCode" content="CATSRULE25" />
    <meta itemprop="availabilityStarts" content="2023-08-06T00:00:00-0800" />
    <meta itemprop="availabilityEnds" content="2023-08-08T23:59:59-0800" />
</div>
{% endhighlight %}
