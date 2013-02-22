---
layout: blogpost
title: ! 'Oracle BPM 10gR3: Six reasons not to use XML Objects throughout your process'
tags:
- 10g
- best practice
- fusion middleware
- oracle bpm
- Oracle BPM
- xml
status: publish
type: post
published: true
meta:
  tumblr_nagazuka_permalink: http://nagazuka.nl/post/1042576728/oracle-bpm-xml-objects-do-not-play-nice
  tumblr_nagazuka_id: '1042576728'
  _edit_last: '1'
---
As with any enterprise software package, sooner or later you’re going to have it make it play nice with XML. Maybe because your process has an XML interface, or you are calling SOAP web services, or because input or output files are formatted using XML. It could be that your company really likes SOA and actually devised a canonical data model, defined in XML Schema, which you have to use for all communication.

Luckily Oracle BPM has some features that make it easy to use XML in your BPM process. By introspecting an XSD schema, you can even use XML objects throughout your BPM process. So you can store XML data as project or instance variables, which alleviates the difficulty of transforming data from BPM objects to SOAP requests. Life’s good, isn’t it?

*No, it isn’t*. It is not a good practice to use XML Objects to in your BPM process. I would strongly advise to use normal BPM objects for all your variables, whether in your methods or as the type of your instance variables. Here are six reasons why:

1. **Tight coupling of interface and code.** 

When your XSD changes, everything is reimported and rebuilt, making it difficult to isolate changes in your version control system.

If you use XML Objects to store and manipulate your instance variables, this means that a change to your XML Schema means a change to almost all your BPM code. The impact of a minor change, such as an addition to an enum in your XSD, means reintrospecting your entire XSD. In Oracle BPM this means that all imported XML Object types are recreated.

When using XML Objects and you want to change the format, for instance to JSON, you’ll have to change all the business logic in your process. This is highly undesirable.

2. **Increased process instance size**

You’ll always want to keep the size of your process instance as low as possible, to avoid memory problems or database storage explosion. XML Objects use far more memory and database storage than regular BPM objects. This is especially the case if you use canonical data models with a large number of elements.

3. **Enum quirks**

Oracle BPM and XML enumerations do not play nice with each other. If you have a variable declared as an xsd:enum type, and you’ll pass it as null, Oracle BPM will choose one of the possible values and fill that in. You can never check for null if it is declared as an xsd:enum type, and this unexpected behaviour can lead to strange bugs.

4. **Harder to make BPM Object Presentations**

Object presentations are perfect for quick prototyping of screens for end-users in the workspace. When using XML Objects, this is more difficult, because you can not add convenience methods, override read or write access.

5. **No object orientation**

You can not add methods to XML Objects, which makes it difficult to use a object-oriented development approach. If you want to manipulate XML Objects in a consistent way, you’ll end up using Helper objects with methods that take an XML Object as input. In my experience, for non-trivial projects this ends up in a mess.

6. **Reimporting bug when combined with SVN**

Oracle BPM has a annoying bug when used with Subversion and BPM Studio. Reintrospecting an existing XSD always goes wrong with this combination of tools. Subversion will complain about some tmp directory that cannot be deleted. The workaround here is to delete your entire XSD catalog and import the XSD from scratch.

#### Conclusion
So how to integrate XML and BPM? I would advise to introspect XSD’s to make it easier to read XML files or call web services. But after calling or reading your XML object, extract the important parts and put those in a normal BPM object. This is lighter, flexible, and decouples your **business** process from the **technical** interface.
