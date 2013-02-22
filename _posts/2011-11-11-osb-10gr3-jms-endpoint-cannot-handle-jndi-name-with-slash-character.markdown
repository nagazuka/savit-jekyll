---
layout: blogpost
title: OSB 10gR3 - JMS endpoint cannot handle JNDI name with slash '/' character
tags:
- Oracle Service Bus
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  _wp_old_slug: osb-10gr3-jms-business-service-cannot-handle-jndi-name-with-slash-character
---
This week I ran into a very annoying bug in Oracle Service Bus 10gR3 when creating a Business Service to send messages to a JMS queue on a remote server.

When configuring the endpoint for a Business Service to put on a JMS queue, the following URI structure should be used:

    jms://hostname:portNumber/jmsConnectionFactoryJNDIName/jmsQueueJNDIName

Seems fairly straightforward, but in this particular case the JNDI name for the Queue Connection Factory contained a 'slash' or / character. For example 'somePrefix/factoryName'. In that case we would end up with the following endpoint URI:

    jms://hostname:portNumber/somePrefix/factoryName/jmsQueueJNDIName

The slash character in a JNDI name is perfectly valid, but OSB can not handle this. When invoking the Business Service this results in an error message:

> The invocation resulted in an error: \[JMSPool:169803\]JNDI lookup of the JMS connection factory somePrefix failed: javax.naming.NameNotFoundException: Unable to resolve 'somePrefix'. Resolved '' \[Root exception is javax.naming.NameNotFoundException: Unable to resolve 'somePrefix'. Resolved ''\]; remaining name 'somePrefix'.

OSB tries to resolve somePrefix as the Connection Factory Name, but of course this does not exist. Unfortunately, there is no way in OSB to escape the slash character to make this work.

There are three solutions to this problem:
* Change the JNDI name to remove the slash. Not always possible if you are dealing with a remote JMS queue.</li>
* In the JMS URI in OSB, replace the '/' with a '.'. For example:
    jms://hostname:portNumber/somePrefix.factoryName/jmsQueueJNDIName  
In JNDI these characters should be equivalent, and I have seen it work in OSB, but this is undocumented. No change to the actual name on the remote queue is required in this case.
* Recommended by Oracle: Create a Weblogic JMS Foreign Server to map a local name (without slash) to the remote queue factory. This creates a new layer of abstraction where OSB only uses internal JNDI lookups, and Weblogic translates this to remote JNDI lookups. Weblogic has no problem with slashes in JNDI names. This solution loosely couples your code with the remote server, but does make your solution more complex. There is also a performance hit, as Weblogic will do the translation lookup everytime a message is sent.
