---
layout: default
title:  "Recursion in Apex"
date:   2019-08-16 00:02:00 -0800
author: Alexander Gimenez
categories: Apex Salesforce
comments: true
---

Note: *The examples of this article are set up directly on the trigger for exemplification purposes, on your orgs please try to set up a proper  TriggerFramework.*

One of the issues developers can face when creating Triggers is Recursion Problems. As we will see this can get far more tricky than it initially seems. But first:
## **What is a recursion problem?**


To find it out we only have to create this little code on a trigger:

```
trigger onAccount on Account (after update) {
List<Account> accountsToupdate = new List<Account> ();
    for(Account A: Trigger.new){
        System.debug('times in the bucle');
            accountsToupdate.add(new Account(Id=A.id,Name='Test'));       
    }
    Update accountsToupdate;
}
```
If we try to update any account either by code or via UI we’ll see something like this:
```
Line: 2, Column: 1
System.DmlException: Update failed. First exception on row 0 with id 0011n00002AzmLlAAJ; first error: CANNOT_INSERT_UPDATE_ACTIVATE_ENTITY, onAccount: execution of AfterUpdate caused by: System.DmlException: Update failed. First exception on row 0 with id 0011n00002AzmLlAAJ; first error: CANNOT_INSERT_UPDATE_ACTIVATE_ENTITY, onAccount: maximum trigger depth exceeded Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate: [] Trigger.onAccount: line 7, column 1: []
```
> They key part is this “maximum trigger depth exceeded Account ” 

This means that the trigger is calling itself again just after executing and again, and again without control, and we’ve reached the maximum depth allowed.

So we call a **recursion problem** when some **parts of our code or processes call themselves** without having proper control in which iteration we’re in and **can end up in unnecessary or even infinite loops.**

***

## **How can we solve this?**
### **First solution**
```
trigger onAccount on Account (after update) {
    List<Account> accountsToupdate = new List<Account> ();
    if(TriggerContextUtility.isFirstRun()){
        for(Account A: Trigger.new){
	System.debug(‘executions’);
            accountsToupdate.add(new Account(Id=A.id,Name='Test'));  
        }   
    }
    update accountsToupdate;
}


public class TriggerContextUtility {

    private static boolean firstRun = true;

    public static boolean isFirstRun() {
        boolean result = firstRun;
        if(result) {
            firstrun = !firstRun;
        }
        return result;
    }
}
```
At this point, we realize some issues:
* Lack of flexibility over the records we want to control its recurrence, since we either enable recurrence over all of them or none.
* Codes runs in chunks of 200 records this solution would only work for <=200 records due to the fact that if we updated 400 records on the first chunk we would put the static variable to false and then on the second chunk would be false and we wouldn’t even do the first iteration for those records.

**Advantages**: 
* Really fast and easy to implement.

**Disadvantages**: 
* Lack of flexibility, doesn’t work for more than 200 records.

\s\s

### **Second solution**
If we can do so, we should establish conditions so we don’t enter again in the trigger. We will mostly exemplify scenarios where you cannot do it by just establishing the modification rules that stop the recursion.
```
trigger onAccount on Account (after update) {
    List<Account> accountsToupdate = new List<Account> ();
    for(Account A: Trigger.new){
        System.debug('executions');
        if(A.name !='Test'){
            accountsToupdate.add(new Account(Id=A.id,Name='Test'));       
        }
    }
    Update accountsToupdate;
    }
```
This doesn’t work though if we have a workflow/process builder that has the same conditions and updates the same record. Because of a corner case in Trigger.old in salesforce behaviour:

*Trigger.old contains a version of the objects before the specific update that fired the trigger. However, there is an exception. When a record is updated and subsequently triggers a workflow rule field update, Trigger.old in the last update trigger won’t contain the version of the object immediately prior to the workflow update, but the object before the initial update was made*

\s\s

### **Third solution**
We assume we have a Workflow active on our object (which will be Account on this example):
- After a recordChanges & When a record is created or changes.

If we want to be able to select which ones re-execute and which ones don’t the best tool would be a Set<Id> that allow us to include there the subselection of records that we want to exclude from being executed again.

Example:
```
trigger onAccount on Account (after update) {
    List<account> accountsToUpdate = new List<Account> ();
    for(Account A: Trigger.new){
        if(!checkRecursive.setOfIds.contains(A.id)){
            if(A.Type == 'Affiliate' && Trigger.Oldmap.get(A.id).Type!=’Affiliate’){
                CalloutClass.dothings(A,accountsToUpdate);
            }
            checkRecursive.SetOfIDs.add(A.id);
        }
    }
    update accountsToupdate;
}

public class checkRecursive {
    public static Set<Id> SetOfIDs = new Set<Id>();
}

```
Ways to expand this solution:

> Use a Map<id,integer> to control the number of times you want to allow recursion and keep track of how many times each record passed by.

> You can have multiple Maps separating records by type or any criteria you want.

* **Advantages**: 
   * Works on most frequent scenarios.
   * Easy to implement.
   * More flexible than the first example.

* **Disadvantages**:
    * Doesn’t cover all scenarios
    
***

## **Which scenarios aren’t we covering?**

* Partial Insert/updates: When we do an operation like:
   * Use Data Loader
   * Any use of Apex Database.update(records,false); True also for the other Database.xxx methods.
   * Bulk, SOAP, or REST APIs that either default AllOrNone to false or set explicitly if available.

*The way Salesforce Platform works is that if a batch insert fails due to one or more records failure and if allOrNone is set to false (which is default for data loader), the platform re-triggers another insert operation with a new batch which contains only the valid/good records (in this case all records except the last one). This second insert operation is actually responsible for inserting the valid records. The first one does not insert any records.* 

### **What happens when we have this kind of insertion/update of records and a workflow rule that updates them?**

- The first insert fails due to one or more records failure and Salesforce makes a subselection of those.
- The second insertion takes place and the subselection is inserted but because the id’s were inserted on the set already on the 1º iteration we don’t do the dml.
- All possible future updates will be ignored since the id of the record is already on the Set<id> of records that we will ignore.

The key to understanding this process is that any STATIC variables we might wanna use are not reliable when some records can succeed and some can fail.

But we still are in trouble if we stop using the static variables since the result would be this:

- 0 Inserts/update on the first iteration since it’s allOrNone=false.
- The trigger does its insert/update.
- Workflow triggers and with its own update the trigger is called again but with the subselection of records that succeeded in 1.
- The trigger does its insert/update again.

***

The solutions to this scenarios are far more complex since we cannot rely on static variables and will be covered in a future article but I hope this general guidelines have helped you so far to know different approaches to solve the recursion problems.

Every org is different and you will face many use cases and business requirements, and there’s no jack of all trades, sometimes a simple approach when you have guarantees of the stability of the architecture can make the job. You have to be flexible and think pragmatically and especially in terms of scalability.  

