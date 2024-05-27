---
layout: post
title:  Create a simple Data Extension search app
description: If users in your instance of Salesforce Marketing Cloud struggle with finding Data Extensions, you can create a CloudPage app to help them navigate folders...
date:   2024-01-24 15:01:35 +0300
image:  '/images/06.jpg'
tags:   [SSJS]
featured: true

{% highlight markdown %}
<table style="padding: 20px;"><tr><td>
<b>How would you like to identify the Data Extension?</b><br>
<form action="%%=RequestParameter('PAGEURL')=%%" method="post">
  <select name="DEprop">
   <option value="Name">Name</option>      
   <option value="CustomerKey">External Key</option>
  </select>
   equals
  <input type="text" name="DEval" value="" maxlength="128"><br>
  <input type="submit" value="Submit">
</form><br><b>Folder path: </b>  
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
    list.unshift(results [0].Name);
        return path(results[0].ParentFolder.ID);
    } else {
        return id;
    }
};
path(FolderID);
Write(list.join("> "));
</script>
</td></tr></table>
{% endhighlight %}