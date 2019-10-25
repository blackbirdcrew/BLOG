---
layout: default
title:  "Calculating pre-signed AWS Signature Version 4 in Salesforce Apex"
date:   2019-10-10 00:02:00 -0800
author: Francesc Pera
categories: AWS Signature Salesforce Apex
comments: true
---
## Calculating pre-signed AWS Signature Version 4 in Salesforce Apex
Amazon Web Services provides a large amount of cloud-based solutions for our applications. And most of the time, they require a Signature to authenticate requests. One of the most common problems is how to calculate that signature in Salesforce.

In this post we will treat AWS v4 signature in query parameters. In following posts we will see how to use this for AWS Simple Queue Service (SQS) and AWS Simple Notification Service (SNS).

[AWS v4 signature](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) takes into account the full request we will be doing. In our case, we will be calculating the signature to be added as a query parameter as stated [here](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html), this is also known as pre-signing a request. This method is called query signed request or pre-signed request. Result of a query signed request to get a file from AWS S3 would be something like this (line breaks added to help reading):
```javascript
https://s3.amazonaws.com/examplebucket/test.txt
?SomeQueryParam1=SomeData
&SomeQueryParam2=SomeMoreData
&X-Amz-Algorithm=AWS4-HMAC-SHA256
&X-Amz-Credential=<your-access-key-id>/20130721/us-east-1/s3/aws4_request
&X-Amz-Date=20130721T201207Z
&X-Amz-Expires=86400
&X-Amz-SignedHeaders=host
&X-Amz-Signature=<signature-value>
```
When doing a query signed request the following parameters are mandatory:
- X-Amz-Algorithm: will always be “AWS4-HMAC-SHA256”.
- X-Amz-Credential: credential scope as “<your-access-key-id>/<yyyyMMdd>/<region>/<service>/aws4_request”.
- X-Amz-Date: actual GMT time formatted like “yyyyMMdd\'T\'HHmmss\'Z\'”.
- X-Amz-SignedHeaders: headers separated by “;”. In pre-signed requests will be only “host” and “content-type” optionally.
- X-Amz-Signature: hex form of the calculated signature.

As we are planning to have many services in our org, we have created a Custom Metadata Object “AWSSetting__mdt” for Service info. Can be substituted by Labels if there is only 1 connection.

To calculate the signature we will need to provide (apart from the request itself):
- AWS Key
- AWS Secret
- Region
- Service (S3, SNS, SQS...)

The process to calculate the signature is as follows:

![V4 SIgnature]({{site.url}}{{site.baseurl}}/pictures/10-10-2019/V4 SIgnature.png)

There are some things to note about the Canonical Request:
- If a line is empty it must be included anyways.
- Canonical URI: if there’s no resource it should be “/”. For S3, here you would define the file you are accessing. Make sure it is URI Encoded at the end of the process.
- Canonical Query String: it must include all query parameters and AuthParams (e.g., X-Amz-Algorithm, etc) but not the signature parameter (as we don’t have it yet).
- Canonical Headers: should only be Host but may require the content type too. Also, every header is to be put in a new line.
- Signed Headers: the same headers present in Canonical headers separated by semicolons.
- If there’s no payload (e.g., for a subscription request) it should be set to an empty string, but it can also be set to “UNSIGNED-PAYLOAD”.

## AWS Abstract Class
First of all we will create an abstract class for AWS connections, which almost always require AWS v4 signing. This is partially based on this [gist](https://gist.github.com/brianmfear/92cf05807ac4becbd21f) from Brian Fear.
```javascript
public abstract class AWS {
    //	Post initialization logic (after constructor, before call)
    protected abstract void init();
	
    //	Things we need to know about the service. Set these values in init()
	protected String host;
	protected String region;
	protected String service;
	protected String resource;
	protected String accessKey;
	protected String payloadSha256;
	protected String contentType;  
	protected Url endpoint;
	protected HttpMethod method;
	protected Blob payload;
    
    //	Not used externally, so we hide these values
	Blob signingKey;
	String signature;
	String canonicalRequest;
	DateTime requestTime;
	Map<String, String> queryParams;
	Map<String, String> headerParams;

	public enum HttpMethod {XGET, XPUT, XHEAD, XOPTIONS, XDELETE, XPOST}

    //	Call this constructor with super() in subclasses
	protected AWS() {
		requestTime = DateTime.now();
		queryParams = new Map<String, String>();
		headerParams = new Map<String, String>();
	}
    
    //	Send a default request
    protected HttpResponse sendRequest() {
		return sendRequest(new Set<Integer> { 200 });
	}    
    
    //	Actually perform the request, and throw exception if response code is not valid
    protected HttpResponse sendRequest(Set<Integer> validCodes) {
        HttpRequest request = createQuerySignedRequest();
    	HttpResponse response = new Http().send(request);

        if(!validCodes.contains(response.getStatusCode())) {
    		throw new ServiceException(response.getBodyDocument().getRootElement());
    	}
    	
        return response;
	}
    
    HttpRequest createQuerySignedRequest(){
        String stringToSign;
        String finalEndpoint;
        String queryString;
        String canonicalHeaders;
        HttpRequest request = new HttpRequest();
		
        init();
        
        request.setMethod(method.name().removeStart('X'));
        payloadSha256 = EncodingUtil.convertToHex(Crypto.generateDigest('SHA-256', payload));
        request.setBodyAsBlob(payload);
        
        if(host == null) {
            host = service + '.' + region + '.amazonaws.com';
        }
        
        request.setHeader('Content-type', contentType);
        setHeader('Content-type', contentType);
        setHeader('Host', host);
        
        //Set AUTHPARAMS in the query
        setQueryParam('X-Amz-Algorithm','AWS4-HMAC-SHA256');
        setQueryParam('X-Amz-Credential', String.join(new List<String> {
								accessKey,
								requestTime.formatGMT('yyyyMMdd'),
								region,
								service,
								'aws4_request' 
							},'/'));
        setQueryParam('X-Amz-Date',requestTime.formatGMT('yyyyMMdd\'T\'HHmmss\'Z\''));
        setQueryParam('X-Amz-SignedHeaders', + String.join(new List<String>(headerParams.keySet()),';').toLowerCase());
        
        //Create the signature
        queryString = createCanonicalQueryString();
        canonicalHeaders = createCanonicalHeaders();
        canonicalRequest = createCanonicalRequest(queryString,canonicalHeaders);
        stringToSign = createStringToSign(canonicalRequest);
        createSignature(stringToSign);
        
        //Add the signature at the end
        queryString += '&X-Amz-Signature=' + signature;
        
        finalEndpoint = new Url(endpoint, resource).toExternalForm() + '?'+ queryString;
        
        request.setEndpoint(finalEndpoint);
        
        return request;
    }    
    
    protected void createSigningKey(String secretKey)
    {
        Blob dateKey = signString(Blob.valueOf(requestTime.formatGMT('yyyyMMdd')),Blob.valueOf('AWS4'+secretKey));
        Blob dateRegionKey = signString(Blob.valueOf(region),dateKey);
        Blob dateRegionServiceKey = signString(Blob.valueOf(service),dateRegionKey);
        signingKey = signString(Blob.valueOf('aws4_request'),dateRegionServiceKey);
    }
    
    //	Create a canonical query string (used during signing)
	String createCanonicalQueryString() {
		List<String> results = new List<String>();
        List<String> keys = new List<String>(queryParams.keySet());
        
		keys.sort();
        
        for(String key: keys) {
            results.add(key + '=' + queryParams.get(key));
        }
                                             
        return String.join(results, '&');
	}
    
    //	Create the canonical headers (used for signing)
	String createCanonicalHeaders() {
        List<String> results = new List<String>();
        List<String> keys = new List<String>(headerParams.keySet());
        
        keys.sort();
		
        for(String key: keys) {
			results.add(key + ':' + headerParams.get(key));
		}
        
		return String.join(results, '\n')+'\n';
	}
    
    // Create the entire canonical request
	String createCanonicalRequest(String queryString, String canonicalHeaders) {
        String result = String.join(
		new List<String> {
			method.name().removeStart('X'),					//	METHOD
			new Url(endPoint, resource).getPath(),				//	RESOURCE
			queryString,							//	CANONICAL QUERY STRING
			canonicalHeaders,						//	CANONICAL HEADERS
               		String.join(new List<String>(headerParams.keySet()), ';'),	//	SIGNED HEADERS
			payloadSha256							//	SHA256 PAYLOAD
		},
		'\n');

        return result;
	}
    
    //	Create the entire string to sign
    String createStringToSign(String canonicalRequest) {
        String result = String.join(
            new List<String> {
                'AWS4-HMAC-SHA256',
                    requestTime.formatGMT('yyyyMMdd\'T\'HHmmss\'Z\''),
                    String.join(new List<String>{requestTime.formatGMT('yyyyMMdd'), region, service, 'aws4_request' },'/'),
                    EncodingUtil.convertToHex(Crypto.generateDigest('sha256', Blob.valueof(canonicalRequest)))
                    },
            '\n'
        );

        return result;
    }
    
    private void createSignature(String stringToSign){
        signature =  EncodingUtil.convertToHex(signString(blob.valueof(stringToSign),signingKey));
    }

    //	We have to replace ~ and " " correctly, or we'll break AWS on those two characters
    protected string uriEncode(String value) {
        return value==null? null: EncodingUtil.urlEncode(value, 'utf-8').replaceAll('%7E','~').replaceAll('\\+','%20');
    }
    
	protected void setHeader(String key, String value) {
		headerParams.put(key.toLowerCase(), value);
	}

    protected void setQueryParam(String key, String value) {
		queryParams.put(key.capitalize(), UriEncode(value));
	}  
    
    private Blob signString(Blob msg, Blob key)
    {
        return Crypto.generateMac('HMACSHA256', msg, key);
    }
    
    //	Turns an Amazon exception into something we can present to the user/catch
	public class ServiceException extends Exception {
		public String Code, Message, Resource, RequestId;
	
		public ServiceException(Dom.XmlNode rootNode) {
			String ns = rootNode.getNamespace();
            Dom.XmlNode node = rootNode.getChildElement('Error', ns);
			Code = XMLUtils.getChildNodeText(node, ns, 'Code');
			Message = XMLUtils.getChildNodeText(node, ns, 'Message');
			Resource = XMLUtils.getChildNodeText(node, ns, 'Type');
			RequestId = XMLUtils.getChildNodeText(rootNode, ns, 'RequestId');
		}
        
        public String toString() {
            return JSON.serialize(this);
        }
	}
}
```

When implementing our class we will be initializing the parameters in the abstract function `init()`. There we will also calculate the signing key showed in the first part of Step 3 of the signature schema.

To perform the request we will then just call the “`sendRequest()`” method.

The endpoint varies depending on the requested service. S3, for example, does not use region while SNS does use it. Check the documentation of the service when creating a new service integration.

## Implementing our class
The implementations of this class will be something like this:
```javascript
public class AWSS3_GetService extends AWS {
        public override void init() {
            resource = '/';
            region = 'us-east-1';
            service = 's3';
            //endpoint = new Url('https://' + service + '.' + region + '.amazonaws.com/');
            endpoint = new Url('https://' + service + '.amazonaws.com/');
            accessKey = 'my-key-here';
            method = HttpMethod.XGET;
            //	Remember to set "payload" here if you need to specify a body
            //	payload = Blob.valueOf('some-text-i-want-to-send');
            //  This method helps prevent leaking secret key, 
            //  as it is never serialized
            createSigningKey('my-secret-key-here');
       }
       public List<String> getBuckets() {
           HttpResponse response = sendRequest();
           List<String> results = new List<String>();
           // Read response XML; if we get this far, no exception happened
           // This code was omitted for brevity
           return results;
       }
    }
*/
```
Here is an example that would calculate a request for a subscription to SNS.
```javascript
public without sharing class AWS_SNS extends AWS{
    static final String SUBSCRIBE_ACTION = 'Subscribe';
    static final String SUBSCRIBE_HTTPS_PROTOCOL = 'https';
    static final String AWS_SERVICE_NAME = 'SNS_EU_Central';
    static final String VERSION = '2010-03-31';
    AWSSetting__mdt AWSService;
    
    public AWS_SNS(){
        AWSService = [SELECT Service__c, 
                             Region__c,
                             AccessKeyId__c,
                             SecretAccessKey__c
                             FROM AWSSetting__mdt
                             WHERE DeveloperName = :AWS_SERVICE_NAME];
    }
    
    public override void init() {
        endpoint = new Url('https://' + AWSService.Service__c + '.' + AWSService.Region__c + '.amazonaws.com');
        resource = '/';
        region = AWSService.Region__c;
        service = AWSService.Service__c;
        accessKey = AWSService.AccessKeyId__c;
        method = HttpMethod.XPOST;
        payload = Blob.valueOf('UNSIGNED-PAYLOAD');
        contentType = 'multipart/form-data';        

        setQueryParam('Version',VERSION);
        createSigningKey(AWSService.SecretAccessKey__c);
    }
    
    public List<String> subscribeToTopic(String topicARN, topicEndpoint) {
        List<String> results = new List<String>();
        
        setQueryParam('Action',SUBSCRIBE_ACTION);
        setQueryParam('Endpoint',topicEndpoint);
        setQueryParam('Protocol',SUBSCRIBE_HTTPS_PROTOCOL);
        setQueryParam('TopicArn',topicARN);
        
        try{            
            HttpResponse response = sendRequest();
        } catch (ServiceException se){
            System.debug('Service Exception: ' + se.toString());
        } catch (Exception e){
            System.debug('Exception: ' + e.getCause() + ' ' + e.getMessage()+ '\n' + e.getStackTraceString());
        }
        
        // Read and treat the response if needed
        
        return results;
    }
}
```
In the “Init()” method we will initialize the AWS variables so our request can be created. If any other class or method within this class needs to perform a different signed call to AWS, just modify the parameters in this method.

When called with “*subscribeToTopic()*” it should generate and send a subscription request like this:
```javascript
https://sns.eu-central-1.amazonaws.com/?Action=Subscribe
&TopicArn=arn%3Aaws%3Asns%3Aus-west-2%3A123456789012%3AMyTopic
&Endpoint=https://ExampleSite.force.com/ExamplePath/services/apexrest/AWSSNS
&Protocol=https
&Version=2010-03-31
&X-Amz-Algorithm=AWS4-HMAC-SHA256
&X-Amz-Credential=AKIAIOSFODNN7EXAMPLE/20130721/eu-central-1/sns/aws4_request
&X-Amz-Date=20130721T201207Z
&X-Amz-SignedHeaders=host;content-type
&X-Amz-Signature=5d672d79c15b13162d9279b0855cfba6789a8edb4c82c400e06b5924a6f2b5d7
```
If our subscription request and it’s signature are correct we will receive a 200-OK response.

Always keep in mind that every call from AWS to Salesforce will consume API Calls from your daily limit which per default is 15000 plus 1000 per Standard Licence per 24 hours (e.g., 10 users = 25000 API Calls  / 24 hours).
