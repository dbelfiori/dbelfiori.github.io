---
layout: post
title:  Use SSJS to work with Lists in SFMC
description: This article explains how to use WSProxy for working with subscriber Lists in server-side JavaScript in Salesforce Marketing Cloud...
date:   2024-01-21 15:01:35 +0300
image:  '/images/11.jpg'
tags:   [SSJS]
featured: true
---
### Basics

#### Create a List
{% highlight js %}
var res = api.createItem("List", {
    ListName: "My NewList",
    Type: "Public",
    ListClassification: "PublicationList",
    Description: "Hello world"
});
{% endhighlight %}

#### Delete a List
{% highlight js %}
var res = api.deleteItem("List", {
    ID: "12345"
});
{% endhighlight %}

#### Retrieve a List
{% highlight js %}
var res = api.retrieve("ListSubscriber", 
    ["SubscriberKey", "ListID", "Status"],
    {
        Property: "SubscriberKey",
        SimpleOperator: "equals",
        Value: "0x000000012345"
    }
);
{% endhighlight %}

#### Retrieve the folder
{% highlight js %}
var req = api.retrieve("List", ["ListName", "Category", "ID"], {
    Property: "ID",
    SimpleOperator: "equals",
    Value: "12345"
});

var catId = req.Results[0].Category;

var req = api.retrieve("DataFolder", ["Name"], {
    Property: "ID",
    SimpleOperator: "equals",
    Value: catId
});

var res = req.Results[0].Name;
{% endhighlight %}

#### Retrieve the folder path
{% highlight js %}
var req = api.retrieve("List", ["ListName", "Category", "ID"], {
    Property: "ID",
    SimpleOperator: "equals",
    Value: "12345"
});

var list = [req.Results[0].ListName];
var id = req.Results[0].Category;

while(id > 0) {

    var req = api.retrieve("DataFolder", ["Name","ParentFolder.ID"], { 
        Property: "ID", 
        SimpleOperator: "equals", 
        Value: id 
    });

    list.unshift(req.Results[0].Name);
    id = req.Results[0].ParentFolder.ID;
}

var res = list.join(" / ");
{% endhighlight %}

### Managing subscribers

#### Add a subscriber to a List
{% highlight js %}
var req = api.retrieve("List", ["ListName", "Category", "ID"], {
    Property: "ID",
    SimpleOperator: "equals",
    Value: "12345"
});

var list = [req.Results[0].ListName];
var id = req.Results[0].Category;

while(id > 0) {

    var req = api.retrieve("DataFolder", ["Name","ParentFolder.ID"], { 
        Property: "ID", 
        SimpleOperator: "equals", 
        Value: id 
    });

    list.unshift(req.Results[0].Name);
    id = req.Results[0].ParentFolder.ID;
}

var res = list.join(" / ");
{% endhighlight %}

#### Add a subscriber to a List
{% highlight js %}
/*
Statuses: Active, Bounced, Held, Unsubscribed, Deleted
*/

var res = api.updateItem("Subscriber", {
    SubscriberKey: "0x000000012345",
    EmailAddress: "jon.snow@crowmail.com",
    Status: "Active",
    EmailTypePreference: "HTML",
    Attributes: [
        {
            Name: "FirstName",
            Value: "Jon"
        },
        {
            Name: "LastName",
            Value: "Snow"
        }
    ],
    Lists: [
        {
            ID: "12345"
        }
    ]
}, 
{
    SaveOptions: [
        {
            PropertyName: '*',
            SaveAction: 'UpdateAdd'
        }
    ]
});
{% endhighlight %}

#### Change subscriber’s status in a List
{% highlight js %}
/*
Statuses: Active, Bounced, Held, Unsubscribed, Deleted
*/

var res = api.updateItem("Subscriber", {
    SubscriberKey: "0x000000012345",
    EmailAddress: "jon.snow@crowmail.com",
    Status: "Unsubscribed",
    Lists: [
        {
            ID: "12345"
        }
    ]
}, 
{
    SaveOptions: [
        {
            PropertyName: '*',
            SaveAction: 'UpdateAdd'
        }
    ]
});
{% endhighlight %}

#### Retrieve all subscribers from a List
{% highlight js %}
var res = api.retrieve("ListSubscriber", 
    [
        "ObjectID", 
        "SubscriberKey", 
        "CreatedDate",
        "ModifiedDate",
        "Client.ID",
        "Client.PartnerClientKey",
        "ListID",
        "Status"
    ],
    {
        Property: "ListID",
        SimpleOperator: "equals",
        Value: "12345"
    }
);
{% endhighlight %}

#### Remove a subscriber from a Suppression List
{% highlight js %}
var req = api.retrieve("List", 
    ["ListName", "ID"],
    {
        Property: "ListName",
        SimpleOperator: "equals",
        Value: "My New Suppression List"
    }
);

var listId = req.Results[0].ID;

var res = api.updateItem("Subscriber", {
    SubscriberKey: "0x000000012345",
    EmailAddress: "jon.snow@crowmail.com",
    Lists: [
        {
            ID: listId,
            Action: "delete"
        }
    ]
});
{% endhighlight %}

### Interacting with Auto-Suppression Lists

#### Create an Auto-Suppression List for a Send Classification
{% highlight js %}
var res = api.createItem("SuppressionListDefinition", {
    Name: "My New Sup List Def",
    Category: 0,
    Description: "Hello world",
    Contexts: [
        {
            Client: {
                ID: Platform.Recipient.GetAttributeValue("memberid")
            },
            Context: "BusinessUnit",
            SendClassificationType: "Marketing",
            AppliesToAllSends: false
        }
    ]
});
{% endhighlight %}

#### Create an Auto-Suppression List for a Sender Profile
{% highlight js %}
var req = api.retrieve("SenderProfile", ["Name", "ObjectID"], {
    Property: "CustomerKey",
    SimpleOperator: "equals",
    Value: "1134"
});

var objId = req.Results[0].ObjectID;

var res = api.createItem("SuppressionListDefinition", {
    Name: "My New Sup List Def 2",
    Category: 0,
    Description: "Hello world",
    Contexts: [
        {
            Client: {
                ID: Platform.Recipient.GetAttributeValue("memberid")
            },
            Context: "SenderProfile",
            SenderProfile: {
                ObjectID: objId
            },
            AppliesToAllSends: false
        }
    ]
});
{% endhighlight %}

#### Add a subscriber to an Auto-Suppression List
{% highlight js %}
var req = api.retrieve("SuppressionListDefinition", 
    [
        "ObjectID",
        "CustomerKey",
        "Name",
        "Description",
        "Client.CreatedBy",
        "CreatedDate",
        "Client.ModifiedBy",
        "ModifiedDate",
        "Category",
        "Client.ID",
        "Client.EnterpriseID",
        "SubscriberCount"
    ],
    {
        Property: "Name",
        SimpleOperator: "equals",
        Value: "My New Sup List Def "
    }
);

var customerKey = req.Results[0].CustomerKey;

var res = api.updateItem("DataExtensionObject", {
    CustomerKey: customerKey,
    Properties: [
        {
            Name: "Email Address",
            Value: "jon.snow@crowmail.com"
        }
    ]
}, 
{
    SaveOptions: [
        {
            PropertyName: '*',
            SaveAction: 'UpdateAdd'
        }
    ]
});
{% endhighlight %}

#### Retrieve all Auto-Suppression Lists
{% highlight js %}
var res = api.retrieve("SuppressionListDefinition", 
    [
        "ObjectID",
        "CustomerKey",
        "Name",
        "Description",
        "Client.CreatedBy",
        "CreatedDate",
        "Client.ModifiedBy",
        "ModifiedDate",
        "Category",
        "Client.ID",
        "Client.EnterpriseID",
        "SubscriberCount"
    ]
);
{% endhighlight %}

#### Retrieve Contexts for an Auto-Suppression List
{% highlight js %}
var res = api.retrieve("SuppressionListContext", 
    [
        "ObjectID",
        "Definition.ObjectID",
        "Definition.Name",
        "Definition.CustomerKey",
        "Definition.Category",
        "Definition.Description",
        "Context",
        "SendClassification.ObjectID",
        "Send.ID",
        "SenderProfile.ObjectID",
        "SendClassificationType",
        "Client.CreatedBy",
        "CreatedDate",
        "Client.ModifiedBy",
        "ModifiedDate",
        "Client.ID",
        "Client.EnterpriseID",
        "AppliesToAllSends"
    ]
);
{% endhighlight %}