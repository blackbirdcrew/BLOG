---
layout: default
title:  " Recursion issues in salesforce and how to solve them"
date:   2019-08-29 00:02:00 -0800
author: Alex Gimenez
categories: Salesforce
comments: true
---

One of the issues developers can face when creating Triggers are recursion problems and how to structure your code. In this article we will address those two issues, taking a look into:
1.	What is a Recursion problem?
2.	What is a Trigger Handler and how to implement it
3.	How to establish recursion control in a simple way.
4.	Why order of execution matters
5.	Another simple way of preventing recursion

Leaving more advanced topics on this subject for a second part of the article since this can get far more tricky than it initially seems.

 Let’s get started!

## What is a recursion Problem?

First of all make sure you’re working on a new salesforce environment like a Trailhead Playground to make this test so we avoid any problems with any other existing code or Workflows (this is really relevant).

To find out what are we referring to when we say recursion, we can create this little code snippet on a trigger:


```
trigger onAccount on Account (after update) {
List<Account> accountsToUpdate = new List<Account> ();
    for(Account accountExample: Trigger.new){
        System.debug('times in the loop);
        accountsToUpdate.add(new Account(Id=accountExample.id,Name='Test'));       
    }
    update accountsToUpdate;
}
```

If we try to update any account, we will get something like this:

**Line: 2, Column: 1
System.DmlException: Update failed. First exception on row 0 with id 0011n00002AzmLlAAJ; first error: CANNOT_INSERT_UPDATE_ACTIVATE_ENTITY, onAccount: execution of AfterUpdate caused by: System.DmlException: Update failed. First exception on row 0 with id 0011n00002AzmLlAAJ; first error: CANNOT_INSERT_UPDATE_ACTIVATE_ENTITY, onAccount: maximum trigger depth exceeded Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate Account trigger event AfterUpdate: [] Trigger.onAccount: line 7, column 1: []**

They key part is the “maximum trigger depth exceeded Account ” message.

First of all, let’s see what our trigger does. It iterates through all the accounts that are being updated and it changes their names to ‘Test’. After that, and this is the root of the problem, we update again the accounts.

As you can imagine, when we update again the accounts, all ‘on update’ triggers on Account will be invoked again, and therefore, our trigger will update again the accounts, which will invoke again the ‘on update’ triggers and so on… So, congratulations, we have created an amazing infinite loop.

In order to avoid useless server usage, Salesforce prevents the infinite loops throwing an exception at the 16th insert/update/delete/undelete call in the same transaction, which is a lot.

If we want more control on Trigger execution, we have to structure our trigger execution, so we should talk first about Trigger Handlers.

## What is a Trigger Handler and how to implement it

In the first example we saw some code in our Account trigger, but this doesn’t follow Salesforce best practices since salesforce clearly states that you shouldn’t create more than 1 trigger per object, therefore if we write multiple triggers logic inside one trigger this would be committing a BLOB antipattern or also known as GOD class. Which basically means we’re storing everything regardless of its functionality in the same place and this goes against software development best practices (if you want to read more about it, click [there] (https://en.wikipedia.org/wiki/Separation_of_concerns)).

To avoid this problem, we should create a method to have multiple business logics outside the trigger itself where we can do a proper separation of concerns.

The trigger handler is an extra class where we handle the different scenarios of the trigger. The good thing about this is that we can follow separation of concerns and avoid having everything in just one enormous BLOB class and control the order in which the orders are executed avoiding having multiple triggers.

Let’s take a look a some basic implementation of a trigger handler:

```
trigger onAccount on Account (after update) {
	AccountTriggerHandler handler = new AccountTriggerHandler();
	if(Trigger.isAfter){
    		if(trigger.isUpdate){
        			handler.afterUpdate(trigger.new,trigger.oldMap);
    		}
	}
}

public class AccountTriggerHandler {
    
    public void afterUpdate(List<Account> triggerNew,Map<id,Account> triggerOldMap){
        List<Account> triggerNewMethod1 = new List<Account> ();
        List<Account> triggerNewMethod2 = new List<Account> ();
        Map<Id,Account> accountsToUpdate = new Map<Id,Account> ();
        // If we're not in the first run we skip it
        
        for(Account triggerAccount: triggerNew){
            // We go through all the accounts choosing only the ones we want to get 
//affected by our business methods, we do it here instead inside the helper so we avoid //repeating iterations over triggerNew
            if(filterMethod1(triggerAccount)){
                triggerNewMethod1.add(triggerAccount);
            }
            if(filterMethod2(triggerAccount,triggerOldMap.get(triggerAccount.id ))){
                triggerNewMethod2.add(triggerAccount);
            }
        }
        //Once we've subselected the accounts for each method we call them
        AccountTriggerHelper.method1(triggerNewMethod1,accountsToUpdate);            
        AccountTriggerHelper.method2(triggerNewMethod2,accountsToUpdate);	 
        
        update accountsToupdate.values();
    }
    
    private boolean filterMethod1(Account accountToFilter){
        return accountToFilter.Type == 'Affiliate';
    }
    
    private boolean filterMethod2(Account accountToFilter, Account oldAccount){
        return accountToFilter.AnnualRevenue != oldAccount.AnnualRevenue; 
    }   
}

public class AccountTriggerHelper {
    public static void method1(List<Account> triggerNew,Map<Id,Account> accountsToUpdate){
        for(Account modifyMySource: triggerNew){
            if(accountsToUpdate.containsKey(modifyMySource.Id)){
                accountsToUpdate.get(modifyMySource.id).AccountSource = 'Affiliate';
            }
            else {
                accountsToUpdate.put(modifyMySource.id,new Account(id=modifyMySource.Id,AccountSource = 'Affiliate'));
            }
        }
    }
    
    public static void method2(List<Account> triggerNew,Map<Id,Account> accountsToUpdate){
        for(Account calculateMyRating: triggerNew){
            if(accountsToUpdate.containsKey(calculateMyRating.Id)){
                accountsToUpdate.get(calculateMyRating.id).Rating = methodToCalculateRating(accountsToUpdate.get(calculateMyRating.id));
            }
            else {
                accountsToUpdate.put(calculateMyrating.id,new Account(id=calculateMyRating.Id,Rating = methodToCalculateRating(calculateMyRating)));
            }
        }
    }
    
    private static string methodToCalculateRating(Account accountToRecalc){

        if(accountToRecalc.AccountSource == 'Affiliate'){
            return 'B';
        }
        else if(accountToRecalc.AnnualRevenue<10000){
            return 'D';
        }
        else if(accountToRecalc.AnnualRevenue <30000){
            return 'C';
        }
        else if(accountToRecalc.AnnualRevenue <10000){
            return 'B';
        }
        else return 'A';
    }
}
```

As you can see the trigger fires on an after update event, then declares the trigger handler and summons the method that corresponds to the event. We can do that for every kind of event the trigger is getting fired on.

Then on the handler we’re filtering inside a for loop. This way we only have to create a list of the accounts that every event with a filter will affect and then pass that list, this way we avoid doing the same for loop for all the modified accounts on every method. And also control the order for example according to our code.

Even if it seems that isn’t directly related to recursion, following best practices when programming , specially following separation of concerns will make our life a lot easier when identifying any kind of issues.

But this code still falls into the same pitfall than the first example provided. So let’s work on a solution so we can execute our trigger.

## How to establish recursion control in a simple way.

So, How can we solve this recurrence loop scenario? Well, this is one of the first intuitive solutions we can find out:

```
public void afterUpdate(List<Account> triggerNew,Map<id,Account> triggerOldMap){
        List<Account> triggerNewMethod1 = new List<Account> ();
        List<Account> triggerNewMethod2 = new List<Account> ();
        Map<Id,Account> accountsToUpdate = new Map<Id,Account> ();
        // If we're not in the first run we skip it
        if(TriggerContextUtility.isFirstRun()){
            
            for(Account triggerAccount: triggerNew){
                // We go through all the account choosing only the ones we want to get //affected by our business methods, we do it here instead inside the helper so we avoid //repeating iterations over triggerNew
                if(filterMethod1(triggerAccount)){
                    triggerNewMethod1.add(triggerAccount);
                }
                if(filterMethod2(triggerAccount,triggerOldMap.get(triggerAccount.id ))){
                    triggerNewMethod2.add(triggerAccount);
                }
            }
            //Once we've subselected the accounts for each method we summon them
            AccountTriggerHelper.method1(triggerNewMethod1,accountsToUpdate);            
            AccountTriggerHelper.method2(triggerNewMethod2,accountsToUpdate);	 
        }
        update accountsToupdate.values();
    }
```
We see one fundamental change and it’s the use of the TriggerContextUtility class, so let’s see it:

```
public class TriggerContextUtility {

    private static boolean firstRun = true;

    public static boolean isFirstRun() {
        Boolean result = firstRun;
        if(result) {
            firstrun = !firstRun;
        }
        return result;
    }
}
```

This class runs a static variable set by default to true that when is consulted through isFirstRun method will be turned to false, and therefore subsequent checks will return false.

So far so good, if we dig deeper we’ll find some issues with this solution. One of them is its lack of flexibility over the records we want to control its recurrence, because we either enable recurrence over all of them or none.

But that’s not the only issue, since code runs in chunks of 200 records this solution would only work for the first 200 records, due to the fact that if we updated 400 records on the first chunk we would put the static variable to false and then on the second chunk would be false and we wouldn’t even do the first iteration for those records.

**Advantages**: Really fast and easy to implement.
**Disadvantages**: Lack of flexibility, doesn’t work for more than 200 records.

## Why order of execution matters

To exemplify the importance of controlling the execution order two brief examples, one where we execute first AccountTriggerHelper.method1 and other where we execute method2.

We go to the Developer console and open an Anonymous Window and paste the following snippet:

```
Account a = new Account(Name='tester',AnnualRevenue = 10000);
insert a;
a.Type = 'Affiliate';
a.AnnualRevenue = 30001;
update A;
a= [SELECT AccountSource,AnnualRevenue,Rating FROM Account WHERE id =: A.id];
System.debug(a);
```

We see the result 

12:55:42:331 USER_DEBUG [7]|DEBUG|Account:{AccountSource=Affiliate, AnnualRevenue=30001, Rating=B, Id=0011n00002Bz09kAAB}

Then if we swap the order on summoning the Helper methods on the Handler we get this result:

12:56:20:293 USER_DEBUG [7]|DEBUG|Account:{AccountSource=Affiliate, AnnualRevenue=30001, Rating=A, Id=0011n00002Bz0AOAAZ}

## Another simple way of preventing recursion

One of the first things we’ve got to keep in mind is to establish conditions to avoid entering again in the trigger **if possible. We will mostly exemplify scenarios where you cannot do it by just establishing the modification rules that stop the recursion.**

For example if we substitute: 

```
private boolean filterMethod1(Account accountToFilter){
        return accountToFilter.Type == 'Affiliate';
    }

For:

private boolean filterMethod1(Account accountToFilter, Account oldAccount){
        return accountToFilter.Type == 'Affiliate' && AccountToFilter.Type != oldAccount.Type;
    }
```

This way we ensure we only include the Accounts that have changed and not only based on the status post update.

So, we’ve delved into trigger structure, potential issues we may encounter if we don’t keep under control our potential recursive calls, but there’s still a lot to cover!

Hope you all learned and enjoyed this article and see you coming back for the second part of it where we will tackle this more tricky and complicated scenarios , with elusive implications and more challenges to overcome!


