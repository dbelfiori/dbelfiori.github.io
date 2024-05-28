---
layout: post
title: Showing & Hiding Desktop/Mobile content
description: Basic code snippet for showing/hiding desktop/mobile content...
date: 2024-03-30 15:01:35 +0300
image: '/images/p_07.jpg'
tags: [Email]
---

This code snippet assumes you have the following two CSS classes in your template.
{% highlight js %}
.mobile-hide { display: none !important; }
.mobile-show { display: block !important; }
{% endhighlight %}

{% highlight markdown %}
<table border="0" cellpadding="0" cellspacing="0" width="100%">
    <tr>
        <td class="mobile-hide">
            Only show on desktop
        </td>
    </tr>
</table>
<!--[if !mso]><!-->
<table border="0" cellpadding="0" cellspacing="0" width="100%">
    <tr>
        <td class="mobile-show" style="display: none;">
            Only show on mobile
        </td>
    </tr>
</table>
<!-- <![endif]-->
{% endhighlight %}