---
title: "InvalidSObjectFault INVALID_TYPE - Managed Package - Partner API - License Issue"
date: 2021-06-23T00:00:00+00:00
draft: false
---

_Creating this entry as the only useful article I found to debug this issue was a comment, on a blog post, from 2013 (Link below)_

## Scenario: 
Use the Partner API to update a custom field, on a custom object, installed via a managed package.

While the integration flow worked correctly in developer sandboxes - Production flow was throwing an error that had not been seen in testing:
```bash
[InvalidSObjectFault [ApiQueryFault [ApiFault exceptionCode='INVALID_TYPE'
exceptionMessage='CUSTOMFIELD_C FROM MANAGED_PACKAGE_OBJECT__C WHERE EXTIDFIELD__C
ERROR at Row:1:Column:41
sObject type 'MANAGED_PACKAGE_OBJECT__C' is not supported. If you are attempting to use a custom object, be sure to append the '__c' after the entity name. Please reference your WSDL or the describe call for the appropriate names.'
```
_(I've replaced the Fields / Object names to example values. CUSTOMFIELD__C is a custom field added to a MANAGED_PACAKGE_OBJECT__C which was installed as part of the managed package.)_



**We made the following assertions:**
- FLS was correctly set for the object via a Permission Set assigned to the Integration User.
- Object create/edit was given to a Permission Set assigned to the Integration Users.

But the integration could still not read/write to the object


## Fix:
To be able to correctly find the Object/Fields via the Partner API - the Integration User **NEEDS** a license to a managed package to be able to interact with the Object/Fields installed via managed package.


### Understanding why:
While in TEST/UAT scenarios these are the previous Integration User + Permission Set, without license assigned, this is because the package at a TEST/UAT level did not need the license assigned to interact with this. (Think how standard Salesforce licesnsing works, only paying for active Users in Produciton - not at a dev level),

In Production, for any user to access the managed package - a user must have a license. In retrospect, this clearly applies to Integration users.

---
Origional blog post from 2013:
[http://sforcehacks.blogspot.com/2013/11/invalidsobjectfault-invalidtype.html](http://sforcehacks.blogspot.com/2013/11/invalidsobjectfault-invalidtype.html)

---
#Salesforce #ManagedPackage #Integrations #PartnerAPI