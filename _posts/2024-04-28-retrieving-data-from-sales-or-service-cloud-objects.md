---
layout: post
title: Retrieving data from Sales or Service Cloud Objects
description: Here's how I use RetrieveSalesforceObjects() to return user data from Sales or Service Cloud... 
date: 2024-04-28 15:01:35 +0300
image: '/images/p_05.jpg'
tags: [Ampscript]
---

Example of how to retrieve user data from a single Salesforce Object.
{% highlight js %}
%%[
SET @AppID = AttributeValue("ApplicationID")

IF NOT EMPTY (@AppID) THEN
  SET @prospectRow = RetrieveSalesforceObjects("User","Id","ID", "=", @AppID)

  IF RowCount(@prospectRow) > 0 THEN
    SET @Row = Row(@prospectRow,1)
    SET @Id = field(@row, "Id")
  ENDIF
ELSE
  SET @OwnerError = 'No Detail Error'
ENDIF

SET @UserRow = RetrieveSalesforceObjects(
  "User",
  "ID,Name,Email,Phone",
  "ID", "=", @ID)

IF RowCount(@UserRow) > 0 then 
  SET @Row = Row(@UserRow,1)
  SET @Name = field(@row, "Name")
  ET @Email = field(@row, "Email")
  SET @Phone = field(@row, "Phone")
ELSE
  SET @OwnerError = 'User Detail Error'
ENDIF
]%%
{% endhighlight %}

An example of if your user could exist in two possible Salesforce Objects.
{% highlight js %}
%%[
SET @AppID = AttributeValue("ApplicationID")
SET @ProspectID = AttributeValue("ProspectID")

IF NOT EMPTY (@AppID) THEN
  SET @AppID = AttributeValue("ApplicationID")
  SET @prospectRow = RetrieveSalesforceObjects("User","Id","ID", "=", @AppID)

  IF RowCount(@prospectRow) > 0 THEN
    SET @Row = Row(@prospectRow,1)
    SET @Id = field(@row, "Id")
  ENDIF
ELSE
  SET @prospectRow = RetrieveSalesforceObjects("User","Id","ID", "=", @ProspectID)
  IF RowCount(@prospectRow) > 0 THEN
    SET @Row = Row(@prospectRow,1)
    SET @Id = field(@row, "Id")
  ELSE
    SET @OwnerError = 'Prospect Detail Error'
  ENDIF

ELSE
  SET @OwnerError = 'No Detail Error'
ENDIF


SET @UserRow = RetrieveSalesforceObjects(
  "User",
  "ID,Name,Email,Phone",
  "ID", "=", @ID)

IF RowCount(@UserRow) > 0 then 
  SET @Row = Row(@UserRow,1)
  SET @Name = field(@row, "Name")
  ET @Email = field(@row, "Email")
  SET @Phone = field(@row, "Phone")
ELSE
  SET @OwnerError = 'User Detail Error'
ENDIF
]%%
{% endhighlight %}