---
layout: post
title: Display dynamic product ratings
description: Ever wanted to show 4 out of 5 stars? Or better yet, 5 out of 5 stars? Here's how... 
date: 2024-05-01 15:01:35 +0300
date:   2020-11-08 15:01:35 +0300
image:  '/images/07.jpg'
tags: [Ampscript, Project]
---

{% highlight js %}
%%[
set @rating = AttributeValue("rating")
set @rating = 4.53 /* Test Value */
set @increment = 0.5
set @ratingNearestHalf = 0
set @ratingBase = 0

/*  https://www.html.am/html-codes/character-codes/html-star-code.cfm
    I'm using &star; &starf; &half; to make my stars, same logic applies when using images.
*/
set @emptyStar = "&star;"
set @fullStar = "&starf;"
set @halfStar = "&half;"
set @ratingOutput = ""

if @rating >= 0 then

  if mod(@rating, 1) > @increment then 
      set @ratingBase = FormatNumber(Subtract(@rating,1), 0)
  else
      set @ratingBase = FormatNumber(@rating, 0)
  endif

  for @s = 1 to @ratingBase do
    set @ratingOutput = concat(@ratingOutput,@fullStar)
  next @s

  set @ratingNearestHalf = Subtract(@rating, mod(@rating, @increment))

  if subtract(@ratingNearestHalf,@ratingBase) > 0.5 then
    set @ratingOutput = concat(@ratingOutput,@emptyStar,@halfStar)
  elseif @ratingNearestHalf != @ratingBase then
    set @ratingOutput = concat(@ratingOutput,@halfStar)
  endif

endif
]%%
{% endhighlight %}

<div class="sample-output">
    <h2>Output:</h2>
    <p>
    rating: 4.53<br>
    increment: 0.5<br>
    mod(rating,1): 0.53<br>
    mod(rating,increment): 0.0300000000000002<br>
    ratingBase: 4<br>
    ratingNearestHalf: 4.5<br>
    ratingNearestHalf - ratingBase: 0.5<br>
    ratingOutput: ★★★★½</p>
</div>
