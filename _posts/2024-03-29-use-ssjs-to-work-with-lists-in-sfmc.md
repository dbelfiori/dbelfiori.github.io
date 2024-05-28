---
layout: post
title: Use SSJS to work with Lists in SFMC
description: This article explains how to use WSProxy for working with subscriber Lists in server-side JavaScript in Salesforce Marketing Cloud...
date: 2024-03-29 15:00:35 +0300
image: '/images/p_02.jpg'
tags: [SSJS]
---
### Get started
The examples of code presented in the next sections will need to be implemented in the following template:

{% highlight js %}
<script runat="server">

Platform.Load("core", "1");

var api = new Script.Util.WSProxy();

try {
    /////////////////////////////////////////////////
    ///// PASTE CODE HERE
    /////////////////////////////////////////////////

Write(Stringify(res));

} catch(error) {
    Write(Stringify(error));
}

</script>
{% endhighlight %}

***

### Basics

#### Create a List
Creating a new List is nice and easy when using WSProxy with the List API object.

{% highlight js %}
var res = api.createItem("List", {
    ListName: "My NewList",
    Type: "Public",
    ListClassification: "PublicationList",
    Description: "Hello world"
});
{% endhighlight %}

#### Delete a List
Deleting a list is a matter of providing the List ID, which can be found in the Email Studio UI. The column is hidden by default, so you will need to activate it.
{% highlight js %}
var res = api.deleteItem("List", {
    ID: "12345"
});
{% endhighlight %}

#### Retrieve a List
In order to retrieve a List
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
First need to fetch the Category ID of the List (called Category, instead of the usual CategoryId) and then use it to retrieve the name of the folder where the List is located.
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
Same here, but in order to retrieve the full path, we need to fetch the ID of the Parent Folder and then loop the process until the Parent Folder ID is equal to 0 (root level folder).

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

***

### Managing subscribers


#### Add a subscriber to a List
The method of adding a new subscriber is a matter of updating or adding a new Subscriber API object and providing one or multiple List IDs to which the subscriber needs to be added.

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
Changing the status of the subscriber is done in the same manner.

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
Retrieving all the subscribers from a List if a matter of querying the ListSubscriber object.

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
For some reason, the List ID of a Suppression List cannot be found in the Email Studio UI. We therefore need to retrieve the List ID based on the name of the List and only then update the Subscriber object’s Lists array.

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

***

### Interacting with Auto-Suppression Lists

#### Create an Auto-Suppression List for a Send Classification
In order to create a proper Auto-Suppression List, we need to provide the Client ID in the Contexts array attribute, which is a fancy name for a Business Unit number (or mid).

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
Same here, but we also need to provide the ObjectID of the Sender Profile we count on using.

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
Adding a new subscriber is done differently for an Auto-Suppression List. The method is the same as adding a new record in a Data Extension.

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
An Auto-Suppression List is basically a SuppressionListDefinition object and has the same structure as a regular Data Extension, except for the SubscriberCount attribute.

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
The SuppressionListContext object defines a context that an Auto-Suppression List can be associated with.

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