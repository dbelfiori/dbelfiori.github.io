---
layout: post
title: Toggling Between Multiple ContentBlocks
description: Create buttons to display an array of ContentBlocks...
date: 2024-06-03 15:01:35 +0300
image: '/images/p_03.jpg'
tags: [Ampscript, SSJS, JS, CloudPage]
featured: true
---

This sample pulls in data from an email into CloudPages inorder to capture a subscribers polling results (@Form_Submission). This snippet can be combined with <a href="/create-a-success-page-on-form-submission">Creating a success page</a>.

{% highlight js %}
<style>
    .content-div { display: none; }
    #content1 { display: block; }
    #buttonRow button {margin: 10px 0; width: 30px;border: 1px solid #ccc;}
</style>
%%[
  Set @id_rowset = BuildRowsetFromString(@module_list,",")
  set @id_row_count = RowCount(@id_rowset)
  if @id_row_count > 0 then

    for @i = 1 TO @id_row_count do
    Set @row = Row(@id_rowset, @i)
    Set @display_product = Field(@row,1)
  ]%%
    <div id="content%%=v(@i)=%%" class="content-div">%%=ContentBlockByKey(@display_product)=%%</div>

%%[ next @i endif ]%%
<div class="button-row" id="buttonRow"></div>

<script runat="server">
    Platform.Load("Core", "1");
    var module_list = Variable.GetValue("@module_list");
</script>

<script>
var module_list = "<script runat='server'>Write(module_list)</script>";
var labelsArray = module_list.split(',');

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