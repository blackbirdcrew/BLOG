---
layout: default
title:  "SOQL Tutorial for Admins"
date:   2019-10-18 00:02:00 -0800
author: Lucas Puig Batlló
categories: SOQL Salesforce
comments: true
---

SOQL stands for Salesforce Object Query Language. It is based on SQL (Structured Query Language) and in general it follows the same structure and syntax with a few exceptions. 

If you’re reading this post you will most likely already know what Salesforce, Objects and Languages are. But what exactly is a Query? 

![Query definition]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/def.png)

SOQL was designed by Salesforce specifically to retrieve information contained in its objects. In an SOQL query, as in every other language, if you want answers you first have to formulate questions in a comprehensible way. Let’s see how this “comprehensible” way of requesting data works in SOQL.

---
Note: *The examples used in this blog post are taken out of a Free Developer org. The only change made to those records, for the purpose of this post, is setting Barcelona as the Billing City in the following accounts:*
- *Edge Communications*
- *Pyramid Construction Inc.*
- *Dickenson plc*
- *GenePoint*

---
# What can an Admin use SOQL for?
As an administrator, more often than not you will have needed to use Workbench to search for specific records, to extract certain sets of data via Dataloader, or even access the Developer Console. All these tools interact with Salesforce via API, and they use SOQL queries to retrieve the data requested by the Admin or the Developer.

Even though most of these tools have a built in wizard that allow you to select what objects, fields or conditions you want to access, it's always useful and interesting to understand the underlying structure of how it works, because it can help us writing more optimized SOQL statements to achieve our goals.

Let's have a look!

---
# How does SOQL work?
For each query, we ask three questions. We have to define what fields we will want to see (**what**), which object we will look for them (**where**), and the conditions we will want the answer to follow (**how**, optional).

In order to join these three questions in an SOQL statement we will need four elements:
1. Clauses
2. API Names
3. Operators
4. Field values

Let’s use an example to explain how these elements are organised and how they interact with the three questions we are asking the Force.com platform.

Imagine we want to see all the Names, Billing Cities and Last Modified Date of all the Accounts.

- **What?** the `Name`, `BillingCity`, `LastModifiedDate` of all the registers (separated by commas)
- **Where?** contained in the `Account` object
- **How?** we can concatenate filters or conditions we want our result to follow with `AND`

The query in this specific example would be:

`SELECT Name, BillingCity, LastModifiedDate FROM Account`

![Query example]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/01.png)

---
# SOQL Clauses used in our example

In our example the clauses are written in capital letters. Let’s analyze the two clauses and the syntax used in our query:

**SELECT (mandatory)**
This clause precedes the API names of the fields we want, separated by commas. As we can see in the result, these fields are now the columns of the Query Result.

**FROM (mandatory)**
This clause precedes the API name of the object we are searching the fields in, in this case Account.

---
# Other SOQL Clauses

**WHERE (optional)**
This clause precedes the condition we are establishing. It can contain: 

- Operators
  - `=` Equals
  - `!=` Not equals
  - `<` Less than
  - `<=` Less than or equal to
  - `>` Greater than
  - `>=` Greater than or equal to
  - `LIKE`, `INCLUDES`, `EXCLUDES` (*we’ll see them in another post*)

- Dates, which follow the format:
  - `YYYY-MM-DD`
  - `YYYY-MM-DDThh:mm:ss+hh:mm`
  - `YYYY-MM-DDThh:mm:ss-hh:mm`
  - `YYYY-MM-DDThh:mm:ssZ`

- Date literals, such as:
  - `TODAY`
  - `TOMORROW`
  - `YESTERDAY` 
  - `THIS_WEEK`
  - `LAST_WEEK`
  - `NEXT_WEEK`
  - ...
  
  Let's see some examples:

**Where example 1**: We want our results to show us all the records that were last modified before the 16th of October 2019.
  
`SELECT Name, BillingCity, LastModifiedDate FROM Account WHERE LastModifiedDate < 2019-10-16T00:00:00.000Z`

![Where example 1]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/02.png)

As we can see, the result is the same as before, but excluding the Accounts that were modified on the 16th of October 2019, to have their Billing City set as Barcelona.


**Where example 2**: We want our results to show us all the records that have Barcelona set as their Billing City

`SELECT Name, BillingCity, LastModifiedDate FROM Account WHERE BillingCity = ‘Barcelona’`

![Where example 2]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/03.png)

---
**ORDER BY (optional)**
This clause precedes:
- API name of the field we want to sorting to be based on
- `ASC` or `DESC`: whether it’s applied in an ascending or descending order
- `NULLS FIRST` or `NULLS LAST` (optional): whether the records with a null value in that field appear first or last


**Order by example 1**: We want our results to be sorted by the Account name, in ascending order

`SELECT Name, BillingCity, LastModifiedDate FROM Account ORDER BY Name ASC`

![Order by example 1]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/04.png)



**Order by example 2**: We want our results to be sorted by the Billing City, in descending order and placing records with a null value in Billing City first

`SELECT Name, BillingCity, LastModifiedDate FROM Account ORDER BY BillingCity DESC NULLS FIRST`

![Order by example 2]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/05.png)

---
**LIMIT (optional)**

This clause precedes the number of records we want to be returned at maximum in the result.


**Limit example 1**: We want our result to show us the first five records that don’t have Barcelona as Billing City

`SELECT Name, BillingCity, LastModifiedDate FROM Account WHERE BillingCity != 'Barcelona' LIMIT 5`

![Limit example 1]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/06.png)

---
**OFFSET (optional)**
This clause precedes the starting row for our data retrieval.


**Offset example 1**: We want our results to show us the first five records that don’t have Barcelona as Billing City, skipping the first two rows.

`SELECT Name, BillingCity, LastModifiedDate FROM Account WHERE BillingCity != 'Barcelona' LIMIT 5 OFFSET 2`

![Offset example 1]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/07.png)

---
# Conclusion

Let's try to build a query combining as many clauses as we can!

`SELECT BillingCity,LastModifiedDate,Name FROM Account WHERE LastModifiedDate >= 2019-10-16T00:00:00.000Z AND BillingCity = 'Barcelona' ORDER BY Name ASC NULLS FIRST LIMIT 3 OFFSET 1`

![Conclusion example 1]({{site.url}}{{site.baseurl}}/pictures/18-10-2019/08.png)

If we're able to write SOQL statements combining all these clauses and operators, we can retrieve data in a fairly optimized way. However, SOQL can get pretty more complicated than what we've learned in this post, and there will be future posts diving deeper into SOQL.
