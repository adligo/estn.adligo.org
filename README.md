# Extensible Services Transport Notation (ESTN)

### Abstract

ESTN is an extension to [EJCN](#ejcn) designed for data transported to and from services. It is designed to reduce the reliance on the transport mechanism or protocol, commonly utilize by [REST](#rest), and separate concerns with greater granularity.  This enhanced separation of concerns allows the ESTN messages to be delivered over [WebSockts](#websockets), [HTTP](#http), [Kafka](#apache-kafka), or plain old [sockets](#sockets).


### Header Extension Details

```
26{ "cmd":"sendMessage" }
Plain Text Message

```

##### Use of ASCI-7/UTF-8

Although not a hard requirement, the use of only [ASCII-7](#ascii)/[UTF-8](#utf8) (NOT UTF-8 extended characters) is highly recommended in the header. This simply maps bytes to characters and reduces processing time.


### Header Keys

Although all header keys are optional, these are the main conventions.

##### cmd

Short for command, the optional <b>'cmd'</b> key identifies how to route this message inside of an application or service. When comparing this protocol to [REST](#rest), it is a partial replacement for the path part of the endpoint. It is not a replacement for the domain name or IP address, as those are abstracted away from this notation.

### Extending the keys

It is generally recommended NOT to include some information about the data in the headers like "dataType":"JSON", but instead simply route your requests to parts of your application or services that know they are going to receive JSON, or a ESTM message or tree.

### Examples

A nested complex ESTN message, note how size is used with the email text command in order to split out binary that happens later in the nested [EJCN](#ejcn) message.

```
24{ "cmd":"sendEmail" }
37{ "part":"emailText", "size": 16 }
Some plain text
43{ "part":"emailAttachment", "size": 53 }
&@#LK!#J%LKHASR@#LKJ
-- not a real image #$%LK#J%^

```

A example with [Ten64](https://github.com/adligo/ten64.adligo.org), note the l after the pound symbol (#) in the first line turns into a 21. Also note, that all [EJCN](#ejcn) parsers are NOT likely to have support for Ten64.

```
#l{ "cmd":"sendData" }
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
35{ "cmd":"sendData", "lines": 3 }
31{ "dateSent": "2026-03-10" }
23{ "sequenceNbr": 3 }
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>

```

### Compatibility with other technologies.

ESTN is designed for swappable transport layers! It should be compatible with [HTTP/1.1](#http), [HTTP/2](#http), [WebSockets](#websockets), [Apache Kafka](#apache-kafka), [Pub/Sub](#pub-sub) and plain old [sockets](#sockets)

# References 

###### Apache Kafka

- [Apache Kafka Official Website](https://kafka.apache.org/)
- [Wikipedia](https://en.wikipedia.org/wiki/Apache_Kafka)
- [Kafka on AWS](https://aws.amazon.com/what-is/apache-kafka/)
- [What is Kafka?](https://www.confluent.io/what-is-apache-kafka/)
- [Where is Kafka going?](https://www.youtube.com/watch?v=9CrlA0Wasvk)

###### ASCII

- [ASCII](https://www.ascii-code.com/)
- [Wikipedia](https://en.wikipedia.org/wiki/ASCII)
- [Table](https://www.asciitable.com/)

###### EJCN

- [EJCN](https://github.com/adligo/ejcn.adligo.org)

###### CSV

- [Wikipedia](https://en.wikipedia.org/wiki/Comma-separated_values)
- [What is a CSV File?](https://www.businessinsider.com/reference/what-is-csv-file)
- [Dr. Nesvit](https://www.youtube.com/watch?v=O0-T-LQgc_o)

###### Google Protocol Buffers

- [Protobuf](https://protobuf.dev/)
- [Wikipedia](https://en.wikipedia.org/wiki/Protocol_Buffers)
- [Github](https://github.com/protocolbuffers/protobuf)
- [Medium](https://medium.com/javarevisited/what-are-protocol-buffers-and-why-they-are-widely-used-cbcb04d378b6)

###### Hexadecimal

- [Wikipedia](https://en.wikipedia.org/wiki/Hexadecimal)
- [Editor](https://cryptii.com/pipes/hex-decoder)
- [Math World](https://mathworld.wolfram.com/Hexadecimal.html)

###### HTTP

- [World Wide Web Consortium](https://www.w3.org/Protocols/)
- [Wikipedia](https://en.wikipedia.org/wiki/HTTP)
- [Cloudflare](https://www.cloudflare.com/learning/ddos/glossary/hypertext-transfer-protocol-http/)
- [Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview)
- [/1 rfc2616](https://datatracker.ietf.org/doc/html/rfc2616)
- [/2 rfc7540](https://httpwg.org/specs/rfc7540.html)

###### JSON

- [JSON.org](https://www.json.org/json-en.html)
- [Wikipedia](https://en.wikipedia.org/wiki/JSON)
- [W3schools](https://www.w3schools.com/whatis/whatis_json.asp)
- [Formatter](https://jsonformatter.org/)
- [Mozilla](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON)

###### JSON Lines

- [Jsonlines.org/](https://jsonlines.org/)
- [Medium](https://medium.com/@sujathamudadla1213/difference-between-ordinary-json-and-json-lines-fc746f93d75e)
- [National Library of Medicine](https://www.ncbi.nlm.nih.gov/datasets/docs/v2/reference-docs/file-formats/metadata-files/why-jsonl/)
- [IBM](https://www.ibm.com/docs/en/taw/1.3.0?topic=analytics-json-versus-json-lines)

###### JSON Schemas

- [Json-schema.org](https://json-schema.org/)
- [Postman](https://blog.postman.com/what-is-json-schema/)
- [MongoDB](https://www.mongodb.com/resources/languages/json-schema-examples)
- [GitHub](https://cswr.github.io/JsonSchema/spec/definitions_references/)

###### Octal

###### Pub/Sub

- [GCP Docs Pub/Sub](https://docs.cloud.google.com/pubsub/docs/overview)
- [GCP Pub/Sub](https://cloud.google.com/pubsub)
- [Wikipedia](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)
- [AWS Pub/Sub](https://aws.amazon.com/what-is/pub-sub-messaging/)
- [Azure Pub/Sub](https://azure.microsoft.com/en-us/products/web-pubsub)
- [Microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/publisher-subscriber)

###### REST

- [Original Paper](https://roy.gbiv.com/pubs/dissertation/top.htm)
- [Wikipedia](https://en.wikipedia.org/wiki/REST)
- [IBM](https://www.ibm.com/think/topics/rest-apis)
- [Roy Fielding](https://en.wikipedia.org/wiki/Roy_Fielding)
- [Understanding the REST Style](https://www.youtube.com/watch?v=w5j2KwzzB-0)

###### Sockets

- [Wikipedia](https://en.wikipedia.org/wiki/Network_socket)
- [IBM](https://www.ibm.com/docs/en/i/7.4.0?topic=programming-how-sockets-work)
- [Oracle](https://docs.oracle.com/javase/tutorial/networking/sockets/definition.html)
- [MIT](https://web.mit.edu/6.031/www/fa17/classes/24-sockets-networking/)

###### UTF8

- [Utf8everywhere.org](http://utf8everywhere.org/)
- [Wikipedia](https://en.wikipedia.org/wiki/UTF-8)
- [Utf8.com](https://www.utf8.com/)
- [Hubspot](https://blog.hubspot.com/website/what-is-utf-8)
- [RFC](https://www.rfc-editor.org/rfc/rfc3629)

###### XML

- [World Wide Web Consortium](https://www.w3.org/TR/xml/)
- [W3schools](https://www.w3schools.com/xml/xml_whatis.asp)
- [Mozilla](https://developer.mozilla.org/en-US/docs/Web/XML/Guides/XML_introduction)
- [Amazon](https://aws.amazon.com/what-is/xml/)
- [Microsoft](https://support.microsoft.com/en-us/office/xml-for-the-uninitiated-a87d234d-4c2e-4409-9cbc-45e4eb857d44)
- [The Library of Congress](https://www.loc.gov/preservation/digital/formats/fdd/fdd000075.shtml)
- [rfc5364](https://datatracker.ietf.org/doc/html/rfc5364)

###### WebSockets

- [Mozilla](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
- [Wikipedia](https://en.wikipedia.org/wiki/WebSocket)
- [Working Group](https://websockets.spec.whatwg.org/)
- [rfc6455](https://tools.ietf.org/html/rfc6455)
