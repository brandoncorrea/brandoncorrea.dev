---
layout: post
title:  "Simplicity in Configurations"
date:   2021-12-16 7:52:05 -0400
categories: apprenticeship
---

There's an interesting thread on C2 about [Occam's Razor][c2] and how it 
relates to software design. Simply put, Occam's Razor is used in favor of
the argument that a simple design is preferable to a complex design.

So if `sum = nums[0] + nums[1]` works, then why bother with 
`for (int i = 0; i < nums.length; i++) sum += nums[i];` if it isn't 
necessary?

### Configuration Files

I was working on adding a configuration file to my HTTP server and had a bit
of trouble deciding on what file format to use. I'm most familiar with
XML and JSON formats, but when I searched for configurations in Java,
the only thing that came up was a `configuration.properties` file.

If you're not familiar with this format, it's basically just a bunch of
`PropertyName=PropertyValue` lines. For a while I was thinking, "This is
_too_ simple. I'm gonna need something more complex than that!" 

After searching a bit longer for configurations, I finally settled for 
this simple file type. It was only later in the day that I realized
this is the _best_ file type for my application.

### Why configuration.properties is Superior

This file type has a few benefits. Let's take a look at each and compare.

````xml
<add key="PropertyName" value="PropertyValue" />
````

````json
{ "propertyName": "propertyValue" }
````

````properties
PropertyName=PropertyValue
````

#### Syntax Errors

First off, you don't really need to worry about syntax errors that can come 
up in XML or JSON formats. As long as you have an `=` sign and provide it a 
name, the configuration will be read.

#### Extra Words

The XML is probably the worst with this. It has all those extra words that 
I have to read just to find out that PropertyName = PropertyValue. JSON
isn't too bad, but there is some filler text here and there that the 
properties file doesn't have.

#### Readability and Changeability

The properties file tells us exactly what we want to know: 
PropertyName = PropertyValue. No brackets, braces, or quotation marks.
This is so simple that even a non-programmer could read, understand, and even
update it quite easily.

#### Configuration Parsing

One final thing to consider with the configurations is how easily they can be
parsed by an application. If we needed to write our own parser, the properties
file would be by far the easiest to read. Just split each line by the `=` sign
and read in every key-value pair.

Because of the simplicity that properties provides, I deem it superior to XML
and JSON, assuming you don't require the additional features that the other
two provide.

[c2]: https://wiki.c2.com/?OccamsRazor
