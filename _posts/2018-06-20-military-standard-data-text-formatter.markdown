---
layout : post
title : "Military Standard Data Text Formatter (MS-DTF)"
date : 2018-6-20 12:31:00
categories : "Programming"
---

In 2015 while on holiday I was asked by my grandfather to help him solve a software problem.
He works for a division of the US Army called [Army MARS](http://www.usarmymars.org/) (Military 
Auxilliary Radio System.) The system they use for communicating over HAM radio not only transmits
messages, but also engineering data in case an error occurs. However, most of the operators
don't use the engineering data, and that data can sometimes obscure the message they're looking
for. I wrote a program using C# which reads the files and separates the engineering data from the
messages. It also archives old messages, automatically checks for new messages, and handles 
multiple message types. The code used for the program has been incorporated into a larger utility.

![MS-DTF]({{ "/assets/images/MSDTF.png" | absolute_url}})