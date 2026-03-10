# Extensible Services Transport Notation (ESTN)

### Abstract

ESTN is a simple, binary, hybrid text, context-free and content-sensitive grammar for encoding messages that will be transported to and from services. It is designed to reduce the reliance on the transport mechanism or protocol, commonly utilize by [REST](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Representational_state_transfer), and separate concerns with greater granularity. It is built around a simple header and body structure; however, it is recursive so that the body may have an ESTN structure as well.

### Structure Overview

ESTN it is comprised of header and body sections. The header section is extremely formal; however, the body can be anything, including [JSON](https://www.google.com/search?q=https://www.json.org/), [XML](https://www.google.com/search?q=https://www.w3.org/XML/), pure binary, and even a child ESTN structure.

### Header Details

The header is comprised of lines or line pairs, each terminated by a [UNIX Line Feed '\n'](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Newline), [ASCII](https://www.google.com/search?q=https://en.wikipedia.org/wiki/ASCII)/[UTF-8](https://www.google.com/search?q=https://en.wikipedia.org/wiki/UTF-8) value of 10 (0x0A in [hexadecimal](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Hexadecimal)). Single lines are nearly identical to the [JSON lines](https://jsonlines.org/) format. However, this format has been optimized slightly for parsing when line pairs exist. In addition, the first character of each line in the header must be one of the following:

##### Header Line First Characters

* **!** The exclamation mark identifies this line as a simple Arabic integer number terminated by a semicolon.
* **#** The pound symbol identifies this line as a [Ten64](https://github.com/adligo/ten64.adligo.org) number terminated by a semicolon.
* **{** The left curly brace identifies this line as a [JSON](https://www.google.com/search?q=https://www.json.org/) line.
* **[** The left square bracket identifies this line as a [JSON](https://www.google.com/search?q=https://www.json.org/) line.

The purpose of lines that start either with an exclamation mark or a pound symbol is simply to identify the number of bytes in the subsequent line. This is a optimization for ESTN parsers. For example, the two in the following code identifies that the subsequent line with '{}' only has 2 bytes;

```
!2;
{}
Plain Text Message

```

```
!24;
{ "cmd":"sendMessage" }
Plain Text Message

```

##### Use of ASCI-7/UTF-8

Although not a hard requirement, the use of only [ASCII-7](https://www.google.com/search?q=https://en.wikipedia.org/wiki/ASCII)/[UTF-8](https://www.google.com/search?q=https://en.wikipedia.org/wiki/UTF-8) (NOT UTF-8 extended characters) is highly recommended in the header. This simply maps bytes to characters and reduces processing time.

### Header Lines

A single line or pair of lines is expected, however this can be changed with the [lines header key](https://www.google.com/search?q=%23lines).

### Header Keys

Although all header keys are optional, these are the main conventions.

##### cmd

Short for command, the optional 'cmd' key identifies how to route this message inside of an application or service. When comparing this protocol to [REST](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Representational_state_transfer), it is a partial replacement for the path part of the endpoint. It is not a replacement for the domain name or IP address, as those are abstracted away from this notation.

##### size

The optional 'size' key identifies an integer, the number of bytes in the body. This can be particularly useful when transporting arbitrary bytes. In particular, for images and other binary data

##### lines

The optional 'lines' key identifies the number of lines or line pairs in the header.

If the first line in this header is a number, indicating that this header has a line pair for the first section, then this indicates the number of line pairs. If the first line in this header is a JSON line, then this indicates the number of JSON lines in the header.

### Extending the keys

It is generally recommended not to include some information about the data in the headers like "dataType":"JSON", but instead simply route your requests to parts of your application or services that know they are going to receive JSON, or a ESTM tree.

### Examples

The simplest ESTN text message, with a empty header;

```
{}
Some plain text

```

A nested complex ESTN message, note how size is used with the email text command in order to split out binary that happens later in the nested ESTN message.

```
!21;
{ "cmd":"sendEmail" }
!34;
{ "cmd":"emailText", "size": 16 }
Some plain text
!40;
{ "cmd":"emailAttachment", "size": 54 }
)&@#LK!#J%LKHASR@#LKJ
-- not a real image #$%LK#J%^

```

A example with [Ten64](https://github.com/adligo/ten64.adligo.org), note the l after the pound symbol (#) in the first line turns into a 21. Also note, that all ESTN parsers are NOT likely to have support for Ten64.

```
#l;
{ "cmd":"sendData" }
{ "name": "George", 
  "age": 23,
  "height": 5.75
}

```

A example with an extended 3 line header.

```
{ "cmd":"sendData", "lines": 3 }
{ "dateSent": "2026-03-10" }
{ "sequenceNbr": 3 }
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>

```

A example with an optimized, extended 3 line header.

```
!33;
{ "cmd":"sendData", "lines": 3 }
!29;
{ "dateSent": "2026-03-10" }
!21;
{ "sequenceNbr": 3 }
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>

```

### Compatibility with other technologies.

ESTN is designed for swappable transport layers! It should be compatible with [HTTP/1.1](https://www.google.com/search?q=https://datatracker.ietf.org/doc/html/rfc7230), [HTTP/2](https://www.google.com/search?q=https://datatracker.ietf.org/doc/html/rfc7540), [WebSockets](https://www.google.com/search?q=https://datatracker.ietf.org/doc/html/rfc6455), [Apache Kafka](https://www.google.com/search?q=https://kafka.apache.org/), [Pub/Sub](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Publish%25E2%2580%2593subscribe_pattern) and plain old [sockets](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Network_socket).

ESTN is designed to be compatible with just about anything, including [XML schemas](https://www.google.com/search?q=https://www.w3.org/XML/Schema), [JSON schemas](https://www.google.com/search?q=https://json-schema.org/), [CSV](https://www.google.com/search?q=https://en.wikipedia.org/wiki/Comma-separated_values) files, [Google Protocol Buffers](https://www.google.com/search?q=https://protobuf.dev/), binary images, zip files that include some of the above in a simple format that is highly configurable and extensible.

### Commentary

ESTN started out as part of [ASBP (Asynchronous Services Bus Protocol)](https://datatracker.ietf.org/doc/draft-adligo-hybi-asbp/). Then for a time, I debated on whether it would use [classification markup notation CMN](https://github.com/adligo/cmn.adligo.org). It was also, partially inspired by the [JSON lines project](https://jsonlines.org/).