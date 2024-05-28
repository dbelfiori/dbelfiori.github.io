---
layout: post
title: Find a Data Extension and it’s folder path using SSJS
description: If you work with multiple Data Extensions in different folders, create a CloudPage app to help navigate... 
date: 2024-01-24 15:01:35 +0300
image: '/images/p_01.jpg'
tags: [SSJS, Project]
featured: true
---
{% highlight js %}
<script runat="server">
Platform.Load("core","1.1.5");
var DEprop = Request.GetQueryStringParameter("DEprop");
var DEval = Request.GetQueryStringParameter("DEval");

var FindDE = DataExtension.Retrieve({Property:DEprop,SimpleOperator:"equals",Value:DEval});
var FolderID = FindDE[0].CategoryID;
var DEname = FindDE[0].Name;

var list = [];
list.push(DEname);

var path = function(id) {
    if (id> 0) {
    var results = Folder.Retrieve({Property:"ID",SimpleOperator:"equals",Value:id});
    list.unshift(results[0].Name);
    return path(results[0].ParentFolder.ID);
    } else {
    return id;
    }
};
path(FolderID);

Variable.SetValue("@search_result", list.join(" > "));
Variable.SetValue("@DEprop", DEprop);
Variable.SetValue("@DEval", DEval);
</script>
{% endhighlight %}
{% highlight markdown %}
<div class="search-form-container">
<h2>How would you like to identify the Data Extension?</h2>
<form class="search-form" action="%%=RequestParameter('PAGEURL')=%%" method="post">
    <div class="search-form__row">
    <select class="search-form__search-type" name="DEprop">
        <option value="Name" %%=IIF(@DEprop == 'Name', 'selected', '')=%%>Name</option>
        <option value="CustomerKey" %%=IIF(@DEprop == 'CustomerKey', 'selected', '')=%%>External Key</option>
    </select>
    <p>equals</p>
    <input class="search-form__search-value" type="text" name="DEval" value="%%=IIF(Empty(@DEval), '', @DEval)=%%" maxlength="128">
    </div>
    <div class="search-form__submit-container">
    <input type="submit" value="Submit">
    </div>
</form>
<div class="search-form__result-container">
    <p class="search-form__result-heading">Folder path:</p>
    <p class="search-form__result">%%=IIF(Empty(@search_result), 'N/A', @search_result)=%%</p>
</div>
</div>
{% endhighlight %}

![]({{site.baseurl}}/images/p_01-1.jpg)