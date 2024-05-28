---
layout: post
title: Loop through product ID's to display content
description: 
date: 2024-05-02 15:01:35 +0300
image: '/images/p_09.jpg'
tags: [Ampscript, Project]
---


{% highlight js %}
%%[
Set @product_id = '44723,44724,44725,44726,44727'

Set @id_rowset = BuildRowsetFromString(@product_id,",")
set @id_row_count = RowCount(@id_rowset)

if @id_row_count > 0 then
    
    for @i = 1 TO @id_row_count do  
    Set @row = Row(@id_rowset, @i)
    Set @value = Field(@row,1)
    ]%%
    
    %%=v(@value)=%%<br>
    
%%[ next @i endif ]%%
{% endhighlight %}

<b>Output:</b><br>
44723<br>
44724<br>
44725<br>
44726<br>
44727