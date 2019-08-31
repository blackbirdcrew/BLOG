---
layout: default
title:  "Integrating Whatsapp using third party API"
date:   2019-08-31 11:00:00 -0800
author: Anton Solomin
categories: Salesforce, Integration
comments: true
---

Introduction

In this tutorial, we’ll set up a Salesforce to Whatsapp integration using a third-party API. Our goal is to be able to send and recieve messages, images and other types of files from Salesforce, store them and to automate the processes. We will also have a general overview of the available options and consider the differences between the native Whatsapp API and third party services.


We'll need a utility class for our needs

	``` JAVA

		public class WhatsAppUtilities {
    
		    public static String sendMessage(String message, String strFrom, String strTo, String accountId, String leadId, String cid) {
		        HttpRequest request = WhatsAppUtilities.prepareChatRequest(message, strFrom, strTo, cid);
		        Http http = new Http();
		        HttpResponse response = http.send(request);
		        return response.getStatus();
		    }
		    
		    @future(callout=true)
		    public static void sendMessageFuture(String message, String strFrom, String strTo, String accountId, String leadId, String cid) {       
		        String response = sendMessage(message, strFrom, strTo, accountId, leadId, cid);
		        if (response == 'OK') {
		            WhatsAppUtilities.createMessage(message, accountId, leadId, cid);
		        } else {
		            /*Expand as needed*/
		            System.debug('Message not created: ' + response);
		        }
		    }

		    public static HttpRequest prepareChatRequest(String message, String strFrom, String strTo, String cid) {
		        String token = 'SAMPLE'; /*Your token goes here*/
		        
		        String url = 'https://www.waboxapp.com/api/send/chat';
		        url += '?token=' + token;
		        url += '&uid=' + strFrom.replace('+','');
		        url += '&to='  + strTo.replace('+','');
		        url += '&text=' + EncodingUtil.urlEncode(message,'UTF-8');
		        url += '&custom_uid=' + cid; 
		        
		        HttpRequest request = new HttpRequest();
		        request.setMethod('GET');
		        request.setEndpoint(url);
		        return request;
		    }

		    public static void createMessage(String message, String accountId, String leadId, String cid) {
		        WhatsAppMessage__c msg = new WhatsAppMessage__c(Account__c = accountId, Lead__c = leadId, Message__c = message, Status__c = 'sent', CID__c = cid);
		        insert msg;
		    }    
		}

	``` 
