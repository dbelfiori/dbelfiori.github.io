---
layout: post
title: Loop through ContentBlock's to dynamically display content
description: Loop through an array of content blocks.
date: 2024-05-02 15:01:35 +0300
date:   2020-11-09 15:01:35 +0300
image:  '/images/post_looping-content.jpg'
tags: [Ampscript]
---

{% highlight js %}
%%[
Set @product_id = '44723,44724,44725,44726,44727'

Set @id_rowset = BuildRowsetFromString(@product_id,",")
set @id_row_count = RowCount(@id_rowset)

if @id_row_count > 0 then
    
    for @i = 1 TO @id_row_count do  
    Set @row = Row(@id_rowset, @i)
    Set @display_product = Field(@row,1)
    ]%%
    
    %%=v(@display_product)=%%<br>
    
%%[ next @i endif ]%%
{% endhighlight %}

<div class="sample-output">
    <h2>Output:</h2>
    <p>
    44723 content displays here<br>
    44724 content displays here<br>
    44725 content displays here<br>
    44726 content displays here<br>
    44727 content displays here</p>
</div>