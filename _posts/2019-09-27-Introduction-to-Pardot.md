---
layout: default
title:  "Introduction to Pardot"
date:   2019-09-27 00:02:00 -0800
author: Laia Fortuny
categories: Pardot Salesforce
comments: true
---

If you are working for a Marketing department in a company seeking to push the online marketing strategy you will probably have heard about Pardot: “B2B Marketing Automation on the World’s #1 CRM”.

Let’s see Pardot in action and how the tool features can be translated into marketing team daily activities and needs.

This is the introductory chapter of a Pardot series blog posts where we will cover the main and coolest functionalities that will help you on the Marketing strategy.

## INTRODUCTION
Salesforce tells us that “Pardot provides a full solution that helps marketers create meaningful connections, generate more pipeline, and empower sales to close more deals”. But how can this be achieved by using Pardot?

Let’s define first which are some of the main objectives of the Marketing team:
- Brand building
- Capture new potential clients (Prospects)
- Know your target client and segment the company’s Prospects database
- Understand which marketing contents (assets) work better
- Nurture existing Prospects with the right assets
- Straight-forward vision and team efforts between Marketing and Sales departments

Pardot tools will definitely help us achieve them:

![Table]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/laiablog1.png)

Let’s see these tools in action:

# Marketing Activity #1: Capture new Leads by using a Layout Template, Landing Page, Progressive Profiling, Sales alerts and a Form
There are hundreds of possibilities to get new Leads using a custom Landing Page:
- Saving a spot for an inhouse event organized to present a new product
- Booking time slot with a sales representative when attending a trade fair
- Registering for a webinar to train on company’s product
- Downloading gated content
- Requesting a call back from a Sales Executive to discuss about the proposed content
- Subscribing to a specific topic distribution list
- Requesting more information about the information provided in the Landing Page

## First stop: build the Pardot Form
<h3>Name section</h3> Which name will identify this Form, where will the asset be stored and which campaign will be associated to (if the first interaction of a new Prospect is this Form, the specified campaign will be assigned to the newly created Prospect).
<h3>Fields section</h3> We must select which fields we want to include in the Pardot Form. A wide variety of considerations must be taken into account: <br/>

<br/>

**a) BASIC TAB**:

![Basic Tab]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/01.png)

1. **Label**: The name of the field as it will be shown to final visitor. 

   Trick: it can include a hyperlink! This is really interesting for GDPR purposes, where you might need to include a link to the ‘Privacy policy, for instance’.
   
   Example: “I have read and accept the <a href=”http://www.xxxx.com/privacypolicy”>privacy policy</a>”

![Privacy Policy]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/02.png)

[2] **Prospect field**: Which Pardot field must the form submitted information map to. 

   Trick: if you click ‘Load Default Data’ button, in case of checkbox, dropdown or radio button, the tab ‘Values’ will be prefilled with the values defined when the Pardot field was created or last updated. These values can be adjusted manually, depending on the constraints given for this specific Form.
   
   Example: If this form is only intended for registering EU trade fair visitors, we might prefill the custom ‘Exclusivity Country’ field with the 27 EU countries, instead of a full world-wide country list.
   
   Example: If this field is a true/false ‘Privacy policy’ acceptance, and the visitor cannot continue submitting his details without accepting this clause, we might decide to only include the “true” option, without any Value label.

![Values]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/03.png)

3. **Type**: Depending on the type selected (checkbox, dropdown or radio button), you can adjust the values displayed in the ‘Values’ tab.

4. **Data format**: If you want to validate some specific input format (only accepting a number format, only accepting email format, only valid email format…), you can select the condition to be met by the input value.

5. **Required**: If marked to true, the visitor will not be able to submit the form without a valid input in the current field.

<br/>

**b) ADVANCED TAB**:

![Advanced Tab]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/04.png)

1. **Error message**: You can customize which ‘Error message’ should see the submitter if the field is left empty or an invalid value is input.

2. **Always display even if previously completed**: If you select this checkbox, this field will always be shown, even if the submitter had filled it before.

   By default, to avoid the Prospect filling again the same information in different forms when using Pardot Forms, if the submitter is using a cookied browser (ie. he has previously submitted a Pardot Form from the same browser) and the field being added to the Form was already completed, it will not be displayed in the Form version for this specific submitter. 

3. **Do not prefill**: If you select this checkbox, this field will not be be prefilled with previous value.

   By default, if you add a field to be shown even if the field was previously completed (as described in the previous point, the field will appear prefilled with the current value. 

   Both this option and the previous one are interesting in terms of GDPR fields, for example, where you always have to request for consent.

   Exception: Text Area fields will never be prefilled, even if the checkbox is left unselected.

4. **Maintain the initial value upon subsequent form submissions**: If you select this checkbox, the data submitted in previous form completions will not be overwritten.

   Example: If a cookied prospect with email ‘cookied@prospect.com’ submits a second form using as email ‘cookiedupdated@prospect.com’, it does not override the existing Prospect information, but creates a new Prospect.

<br/>

**c) PROGRESSIVE TAB**:

![Progressive Tab]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/05.png)

1. **Show this field only if the prospect already has data in the following field(s)**: If you select this checkbox and select the corresponding fields, the field currently being set up will only appear if the selected fields in this tab are already filled in the Prospects record.

   We want to avoid never-ending forms with dozens of fields that could discourage the visitor from filling and submitting it. It is better to take advantage of this Pardot functionality, so that this field being currently set up, will only appear in the visitor form if the cookied Prospect has previously filled the mentioned fields in this tab. 

   We will prioritize gathering the other fields (both using this form or any other Pardot form) and we will only ask for this extra segmentation details after the other information is already in our records.

2. **Dependent**: Display other fields in this form based on the value of this field: If you select this checkbox and the field input value from the visitor equals the criteria defined in this tab, the field(s) added in this tab will be shown to the visitor.

   We want to avoid showing unnecessary fields. If a specific field only applies to Prospects who select a specific value in another field, we can use this functionality.

   Example: If we have a field ‘Interested in’ with the relevant business lines of the company and a ‘Product’ field only applicable to the business line ‘Consultancy’, when configuring the field ‘Interested in’, we will define that the field ‘Product’ should only be shown if the field equals to: “Consultancy”.

<h3>Look and Feel section</h3> We must choose:

<br/>

1. **Layout Template**, which will define the styling of the form (we will cover this topic in a future blog post)

2. **Submit Button Text**: The call-to-action that will appear in the button

![Submit Button Text]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/06.png)

3. **Above Form**: The description appearing before form fields

4. **Below Form**: The description appearing after form fields

![Below Form]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/07.png)

5. **Styles**: Some of the CSS styling can be configured without coding (eg. you can define if a * symbol should appear next to required fields)

6. **Advanced**:
   1. Kiosk/Data Entry Mode: Do not cookie browser as submitted prospect → This functionality should be used if the browser (from where the form will be submitted) will not be a final user device (eg. registration desktop computer in a trade fair)
   
   2. Include "Not you?" link to allow visitors to reset the form → Allowing a way out if the browser was previously used to submit data for another Prospect. When clicking this option, the visitor will be able to fill the Form from scratch (without being linked to the previous cookied Prospect in that browser)
   
   3. Enable explicit bot protecting using reCAPTCHA → This functionality adds an additional layout of protection to avoid bot attacking the form and creating unlimited fake Prospects.

## Second stop: build the Landing Page

1. **Name**: Which name will identify this Landing, where will the asset be stored and which campaign will be associated to (if the first interaction of a new Prospect is this Landing, the specified campaign will be assigned to the newly created Prospect).
2. **Select Form**: We must select which Pardot Form we want to use.
3. **Content Layout**: We must choose between ‘My Layout Templates’ (created previously) or ‘Stock templates’ (basic WYSIWYG editor).
4. **Landing Page Content**: Depending whether we have selected ‘My Layout Templates’ or ‘Stock Templates’, we will be able to perform differently here. 
   In the first case, we will be able to modify all the editable areas (if defined in the Layout Template as ‘pardot-region’).
   While in the second case, we will be able to adjust and add new elements using the WYSIWYG editor (be careful, not responsive format!).
   
![Landing Page Content]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/08.png)
   
5. **Confirm & Save**: Review the details and see the landing page preview.

___

## Last stop: publish the Content

![Pardot]({{site.url}}{{site.baseurl}}/pictures/27-09-2019/09.png)

Now that the marketing content is ready to share, we can now push traffic towards it! We can:
* Post the ‘Link’ URL in the social media more relevant (you can schedule the posting from Pardot if you have the LinkedIn, Twitter and / or Facebook connectors enabled).
* Use the ‘Link’ URL as the redirect URL for the social media paid campaigns (therefore, once a visitor clicks on the ad banner, he will be redirected to this targeted landing page).
* Send a List Email to a targeted distribution list including a call-to-action button that redirects to this landing page.
