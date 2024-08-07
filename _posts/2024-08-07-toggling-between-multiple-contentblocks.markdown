---
layout: post
title: Toggling Between Multiple ContentBlocks
description: Create buttons to display an array of ContentBlocks.
date:   2024-08-07 15:01:35 +0300
image:  '/images/02.jpg'
tags: [Ampscript, SSJS, JS, CloudPage]
---

This sample pulls in and toggles between multiple content blocks by Customer Key (@content_list).
{% highlight js %}
<style>
    .content-div { display: none; }
    #content1 { display: block; }
    #buttonRow button {margin: 10px 0; width: 30px;border: 1px solid #ccc;}
</style>
%%[
  Set @content_list = 'ContentBlock1,ContentBlock2,ContentBlock3'

  Set @id_rowset = BuildRowsetFromString(@content_list,",")
  set @id_row_count = RowCount(@id_rowset)
  if @id_row_count > 0 then

    for @i = 1 TO @id_row_count do
    Set @row = Row(@id_rowset, @i)
    Set @display_content = Field(@row,1)
  ]%%

<div id="content%%=v(@i)=%%" class="content-div">%%=ContentBlockByKey(@display_content)=%%</div>
%%[ next @i endif ]%%
<div class="button-row" id="buttonRow"></div>

<script runat="server">
    Platform.Load("Core", "1");
    var content_list = Variable.GetValue("@content_list");
</script>

<script>
var content_list = "<script runat='server'>Write(content_list)</script>";
var labelsArray = content_list.split(',');

var buttonRow = document.getElementById('buttonRow');

labelsArray.forEach((label, index) => {
    var button = document.createElement('button');
    button.textContent = index + 1;
    button.addEventListener('click', () => {
        document.querySelectorAll('.content-div').forEach(div => div.style.display = 'none');
        document.getElementById(`content${index + 1}`).style.display = 'block';
    });
    buttonRow.appendChild(button);
});
</script>
{% endhighlight %}

<p>Output:</p>
<style>
    .content-div { display: none; }
    #content1 { display: block; } /* Show content1 by default */
    #buttonRow button {margin: 10px 0; width: 30px;border: 1px solid #ccc;}
</style>

<div id="content1" class="content-div">Display ContentBlock1 Content</div>
<div id="content2" class="content-div">Display ContentBlock2 Content</div>
<div id="content3" class="content-div">Display ContentBlock3 Content</div>
<div class="button-row" id="buttonRow"></div>
<script>
    var content_list = "ContentBlock1, ContentBlock2, ContentBlock3";
    var labelsArray = content_list.split(',');

    var buttonRow = document.getElementById('buttonRow');

    labelsArray.forEach((label, index) => {
        var button = document.createElement('button');
        button.textContent = index + 1;
        button.addEventListener('click', () => {
            document.querySelectorAll('.content-div').forEach(div => div.style.display = 'none');
            document.getElementById(`content${index + 1}`).style.display = 'block';
        });
        buttonRow.appendChild(button);
    });
</script>