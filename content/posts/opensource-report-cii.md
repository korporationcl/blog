---
title: "Open source software report by the CII"
date: 2020-02-22T20:51:19+01:00
draft: false
tags: ["cloud", "opensource", "english", "linux foundation", "core infrastructure initiative"]
---

# What the hell is the CII

The CII aka `Core Infrastructure Initiative`, is a foundation backed up by companies such as `Amazon Web Services`, `Google`, `Facebook`, `Cisco`, `Intel`, among some. 

CII's report states that Open Source software has become **critical** to sustain the moderm economy, and contitutes between **80-90%** of any give piece of moderm software.

You may be wondering, how the CII helps here and the answer is: **they provide funding ($) and support to OSS projects that are critical to support global infrastructure**.

I believe it's worth mentioning that in the first version of this report (Census I, 2015), they identified which packages in `Debian` were the most critical to support all the `Kernel` **operations** and **security**. Unfortunately in that version they could not identified which package are actually deployed in `production` environments, therefore in the `mid-2018`, they partnered with the `Harvard University` to get a more complete view in the usage of the OSS in public and private organizations.

# Why though?

> It is difficult to fully understand the health and security of FOSS because 1) FOSS, by design, is distributed in nature so there is no central authority  to ensure quality and maintenance, and 2) because FOSS can be freely copied and modified, it is unclear how much FOSS, and precisely what types of FOSS, are most widely used. Therefore, to ensure the future health and security of the FOSS ecosystem, it is critical to understand what FOSS is being used, and how well it is supported and maintained

There is always a reason and I was wondering, why would they allocate time and resources to conduct this type of research (although as an enthusiast I have always been curious about it), but everything started because of the [HeartBleed](https://heartbleed.com/) vulnerability and the sub-sequents attacks.

If there is a key takeaway from this part of the report I would say:

> Unfortunately, vulnerabilities in other widely-used projects with smaller contributor bases, like OpenSSL, can slip by unnoticed.

But regardless of that, the idea of this report is to inform and take action (inspire people to contribute and audit the software you are using). Also, in order to get this data, companies such as `Facebook` let's say, need to **share** their data about the software they use (and their dependencies), however, the data published has been **obscured** or **filtered** to not compromise the businesses.

Another thing to consider is the number of `downloads` and their public statistics does not really confirms the usage in `production` environments of that software, mostly indicates popularity; in fact, the results are very surprising for non-javascript packages.

# The winners are

If you skipped all the boring introduction because you just want to know the numbers, the **preliminary** results are divided in `10 most used packages` and the `10 most used non-javascript packages`.

## Most used packages (javascript)

- [async](https://github.com/caolan/async): A utility module which provides straight-forward, powerful functions for working with asynchronous JavaScript. Although originally designed for use with Node.js and installable via npm install async, it can also be used directly in the browser.

- [inherits](http://github.com/isaacs/inherits): Browser-friendly inheritance fully compatible with standard node.js inherits.

- [isarray](http://github.com/juliangruber/isarray): Array#isArray for older browsers and deprecated Node.js versions.

- [kind-of](http://github.com/jonschlinkert/kind-of): Get the native JavaScript type of a value.

- [lodash](http://github.com/lodash/lodash): A modern JavaScript utility library delivering modularity, performance & extras.

- [minimist](http://github.com/substack/minimist): Parse argument options. This module is the guts of optimist’s argument parser without all the fanciful decoration.

- [natives](http://github.com/addaleax/natives): Do stuff with Node.js’s native JavaScript modules.

- [qs](): A querystring parsing and stringifying library with some added security.

- [readable-stream](http://github.com/nodejs/readable-stream): Node.js core streams for userland.

- [string_decoder](http://github.com/nodejs/string_decoder): Node-core string_decoder for userland.

## Most used non-javascript
 
- [jackson-core](github.com/FasterXML/jackson-core): A core part of Jackson that defines Streaming API as well as basic shared abstractions.

- [Guava](http://github.com/google/guava.git): Google core libraries for Java.

- [commons-codec](http://github.com/apache/commons-codec): Apache Commons Codec (TM) software provides implementations of common encoders and decoders such as Base64, Hex, Phonetic and URLs.

- [commons-io](http://github.com/apache/commons-io): Commons IO is a library of utilities to assist with developing IO functionality.

- [httpcomponents-client](http://github.com/apache/httpcomponents-client): The Apache HttpComponents™ project is responsible for creating and maintaining a toolset of low level Java components focused on HTTP and associated protocols.

- [httpcomponents-core](http://github.com/apache/httpcomponents-core): The Apache HttpComponents™ project is responsible for creating and maintaining a toolset of low level Java components focused on HTTP and associated protocols. 

- [logback-core](http://github.com/qos-ch/logback): The reliable, generic, fast and flexible logging framework for Java. 

- [commons-lang3](http://github.com/apache/commons-lang): A package of Java utility classes for the classes that are in java.lang’s hierarchy, or are considered to be so standard as to justify existence in java.lang.

- [slf4j](http://github.com/qos-ch/slf4j): Simple Logging Facade for Java. (makes sense the description)


---

# Lessons learned so far by CII

I did not want to finish this short writing without making a reference to the takeways they found during this awesome work, which can be kind of surprising and concerning from a security point of view:

- The need for an standarised `Naming` schema for Software components.
- **7/10** most used projects where hosted under **personal** Github accounts.
- Persistence of legacy systems (not releasing features only maintaining old stuff).

# Source

- https://www.coreinfrastructure.org/programs/census-program-ii/
