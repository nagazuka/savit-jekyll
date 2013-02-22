---
layout: blogpost
title: Use Oracle Service Bus 11g as a Templating Engine with Apache Velocity
tags:
- 11g
- Apache Velocit
- Fusion Middleware
- Java Callout
- Oracle Service Bus
- OSB
- Tutorial
- Web Service
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  _thumbnail_id: '43'
---
## Goal
* Create a Web Service with Oracle service Bus 11g that renders a template with the Apache Velocity templating engine
* Learn about Java callouts in OSB with dependencies on external libraries
  
### Why?

For example: To render templates for e-mails to send out with Oracle Service Bus 11g.

With Apache Velocity you have a well-known and widely-used templating engine, to render for instance HTML content. After rendering this content can be uploaded with FTP or e-mailed with OSB.
  
### Overview

The diagram below shows the overview of the components needed to make this work.

<a href="http://www.nagazuka.nl/wp-content/uploads/2011/07/tumblr_llfxfvretG1qa552a.png"><img class="size-full wp-image-43 alignnone" title="osb_velocity_overview" src="http://www.nagazuka.nl/wp-content/uploads/2011/07/tumblr_llfxfvretG1qa552a.png" alt="Overview of OSB and Apache Velocity integration" width="500" height="298" /></a>
  
### How?
  
#### Prerequisites:
* Weblogic 11g (10.1.3.5.0) installed with Oracle Service Bus 11gR1 (11.1.1.4.0)
* Oracle Service Bus 11g domain configured and running
* Oracle Enterprise Pack for Eclipse
  
#### Steps

For development I use the Oracle Enterprise Pack for Eclipse, which is downloadable from OTN.

##### 1 Create a TemplateUtilities JAR for use in Oracle Service Bus
* Create new Java Project **TemplateUtilities**
* Add the following JARs as external libraries to Build Path of the Eclipse project  
    * Apache Velocity jar: velocity-1.7-dep.jar
    * XMLBeans jar: xmlbeans-1.0.3.jar

The Apache Velocity jar will serve as our templating engine, while XMLBeans is required because we are using an XML message for the Java callout input.
* Add a new class that will serve the Java callout in OSB called <span><a title="TemplateUtilities.java" href="https://github.com/nagazuka/OSBWithVelocityTemplating/blob/master/TemplateUtilities/src/nl/nagazuka/service/example/template/TemplateUtilities.java" target="_blank">TemplateUtilities.java</a></span>**
**

This code reads the XML request:
    String templateName = getTemplateName(renderTemplateReq);
    Map<String, String> params = getParameters(renderTemplateReq);

The XML input for the renderTemplate method should be formatted as:

  <renderTemplate>
    <templateName>RenderMe.vm</templateName>
      <parameters>
        <parameter>
          <key>VARIABLE_NAME</key>
          <value>VALUE</value>
        </parameter>
      </parameters>
  </renderTemplate>

It looks for **velocity.properties** (configuration for Apache Velocity on where to find the templates) **on the classpath**
    URL url = ClassLoader.getSystemResource(VELOCITY_PROPERTIES);
  
In the last part, the Velocity template is rendered with the supplied parameters and a String with the result is returned.
    Velocity.mergeTemplate(templateName, “UTF-8”, context, w);
    return w.toString();
  
##### 2 Export Java project from Eclipse to JAR file so we can use it in Oracle Service Bus 11g

In the Project Explorer, right-click and select Export -> Export as Service Bus JAR file.
  
##### 3 Create a WSDL that defines the interface of our new webservice **TemplateService_v1.wsdl**

* Input: template name and list of key-value parameters
* Output: a string with the rendered template
  
##### 4 Create a new Proxy Service **TemplateService_v1**
*	Create a new Oracle Service Bus Configuration Project to contain our new Project
*	Create a new Oracle Service Bus Project in the Configuration Project
*	Create a new Proxy Service **TemplateService_v1 **using our created WSDL binding
*	Add **TemplateUtilities.jar** to TemplateService project
*	In the message flow add a Pipeline pair in which we will process the request
* Add a Stage in the request pipeline with two actions:
    *	Add an Assign action to create the request to the variable **$renderTemplateRequest**. For simplicity let’s do it hardcoded (use example request above).
    *	Add a Java callout that uses the TemplateUtilities:
        1. Select the renderTemplateMethod 
        2. Use $renderTemplateRequest as the input
        3. Assign Result Value to $renderTemplateResponse
![Java Callout Configuration](http://www.nagazuka.nl/wp-content/uploads/2011/07/tumblr_llfxrcRrxo1qa552a.png)
*	Add a Stage in the response pipeline with an Assign action that inserts the rendered template in to the service response
![OSB Proxy Service Configuration with Java Callout](http://www.nagazuka.nl/wp-content/uploads/2011/07/tumblr_llfxpawFfc1qa552a.png)

##### 5. Deploy and test 
* Deploy using Export to Oracle Service Bus - Resources to Server
* Execute with Test Console

Complete code can be found on [nagazuka's Github](https://github.com/nagazuka/OSBWithVelocityTemplating/).
