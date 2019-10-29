---
layout: default
title:  "Custom lookup lightning web component"
date:   2019-10-25 00:02:00 -0800
author: Lucia Lillo
categories: Lightning component Web custom lookup
comments: true
---

## Introduction
In this tutorial we will create a Lightning web component that mimics the standard lookup behaviour, it should be reusable by any Salesforce environment. The main goal is to achieve a better understanding of the LWC framework and be able to identify its main features and describe them through this example.

The LWC framework appeared on Spring’19 release and can coexist with aura components on a page. It provides an easy to work with framework since its main languages are Javascript and HTML.

## Requirements to be met
- Search by several fields at a time
- Scalability of the component to any object and any Salesforce org
- Be able to prefill the lookup upon creation
- Be able to scroll through the results via keyboard
- Adopt as much as possible the standard lookup field look and feel

Taking into account the previous requirements and adding some constraints the following are the properties needed for the component definition.

- **objectName**: Mandatory. The API Name of the sObject to be queried.
- **fields**: Mandatory. A comma separated list of all the sObject fields to be searched through.
- **label**: Mandatory. The label to be displayed for the lookup field.
- **iconName**: Mandatory. The icon name to be displayed in each search entry, must be a Salesforce icon - e.g. action:new_account.
- **selectedRecordId**: Optional. To default the value of the lookup field.
- **selectedRecordName**: Optional. To default the value of the lookup field.
- **placeholder**: Optional. The placeholder of the lookup field, if not defined it will default to the one defined in the system label.
- **Required**: Optional. By default it will be set to false.
- **inputReadOnly**: Optional. By default it will be set to false.

Those are the properties that will be exposed since they should be accessible when defining the component. In LWC the *@api* decorator can be used to expose any property making them available as attributes in the component markup.

Also the result of each operation made on the lookup model should be available on the HTML template and a rerender done whenever it is updated. In other words, the *@track* decorator must be used to track the *result* attribute. The remaining properties that should be private and do not affect the render on the HTML template should be left without decorators.

All the properties and functions will be defined within the JavaScript class of the component and will then be used in the template defined in the HTML file. Data binding in LWC HTML template is as simple as it can get, just use *{propertyName}* to reference a property defined in the JavaScript class.

`TOBESUBSTITUTED_BYCustomLookupjs`

Also note the easiness of code blocks dynamic rendering in LWC thanks to if:true/if:false directives.

`TOBESUBSTITUTED_BYCustomLookuphtml`

The logic of the lookup model has been encapsulated into the lookup LWC that is made up of only the JavaScript class. In it the properties available for each lookup instance have defined and also the functions available. Once defined, the CustomLookup LWC imports the Lookup LWC and creates an instance of it as a property of our component, it will be the *lookup* attribute.

`TOBESUBSTITUTED_BYLookupjs`

Be mindful of the select function defined in the lookup LWC since it serves of event handling. It will dispatch a new *selected* custom event whenever a user selects a record from the list. To pass data from the event dispatcher to the event listener make use of the *detail* property when constructing the custom event.

Once the dispatcher is configured let’s create a listener that will handle the data dispatched and treat it as needed. LWC allows the use of custom event listeners when creating the component by defining the attribute *onCustomEventName* in the component markup. In this example we will listen to the selected custom event, if it fires the *selectPayer* method from the component container will be called. 

This approach allows the CustomLookup LWC to fire the correct parent action after an event is fired. Check the following code for the component markup and available attributes.

```
<c:customLookup onselected="{!c.selectPayer}" objectName="Account" 
fields="Name,DocumentNumber__c" label="Payer" iconName="action:new_account" 
selectedRecordId="{!v.payerId}" selectedRecordName="{!v.payerName}" placeholder="Search a 
payer..." required="false" inputReadOnly="false"/>
```

The action fired by the listener will have access to the data defined in the *detail* property of the CustomEvent. In this case the *id* property was defined inside the *detail*.

```selectPayer : function(component, event, helper){        
    component.set("v.payerId", event.getParam('id'));
},
```

Once the main ins and outs have been explained [here](https://youtu.be/pCSnZ2XsC8c) is a demo of the final component once implemented. Hope you like it!

## Highlights of LWC
- Ability to import Salesforce modules directly into your JavaScript file.
- Decorators to extend the functionality of the properties of the JavaScript class.
- LWC provides several methods to call an Apex method:
- Property wires: it automatically routes the result of the method to the property (makes use of the *@wire* decorator).
- Function wires: The result of the method is passed onto a callback function (makes use of the *@wire* decorator).
- Imperative calls: import an apex method into your JavaScript file and call it manually from your code (This method is used in the lookup LWC for retrieving the search results).
- The LWC framework provides a series of lifecycle hooks such as those available in other frameworks like Vue.js or React.js. (e.g. the *connectCallback()* is being used to load additional styles once the component is inserted in the DOM).

## Conclusions
The LWC framework increases the performance of applications when compared to Aura and is more intuitive from a developer’s point of view since basically the languages used are JavaScript and HTML with very few limitations. Also there is a decrease in the amounts of Apex code needed since you can import modules directly into your JavaScript which will allow you to have lots of standard functionality from the start.

Find the whole CustomLookup component in github. For complete information on the LWC go to the [Lightning Web Component Dev Guide](https://developer.salesforce.com/docs/component-library/documentation/lwc).

