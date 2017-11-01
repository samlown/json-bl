# JSON-BL
## JavaScript Object Notation for Business Language

Because JSON is universal.

NOTICE: THIS PROJECT UNDER EXPERIMENTAL DEVELOPMENT.

## Introduction

JSON-BL is a consensus based standard to create and communicate documents relating to business. The key focus is simplicity and portability, but with advanced security definitions built into the language to deal with complex issues like digital signatures, digests, and encryption.

Application interoperability for business related documents is tough as there are few standards written in a clear and concise format that take into account regional variations. These issues are becoming even more acute as web services sprout up offering more consumer choice. JSON-BL attempts to ease some of the pains by defining a set of [JSON Schema](http://json-schema.org) that can be used to export, import, and send business documents between accounts and projects. It should be easy for example to send an invoice from your accounting platform to that of your client and have that data automatically validated with a digital signature and stored for an accountant to review later.

A major part of any great standard are the tools that are available to build and validate the documents it defines. JSON-BL tries to avoid the usual disconnection between the tools and specification by providing libraries for multiple platforms from the start, as part of the standard. It should be easy to build and parse JSON-BL documents that require only a few extra steps to take advantage of complex yet important features in business like digital signatures.

The JSON-BL Standard is essentially composed of:

 * JSON Schema for building business objects, the information building blocks,
 * a document definition, for sending the objects from one place to another securely, and
 * multi-platform libraries, for validating and building documents.

Much of the standard is of course based on the work of others in standards such as SOAP and UBL.


## Contributing

JSON-BL is a collaborative project. If you'd like to see your own schema or a change implemented, make the change yourself and send a github pull request.


## Vocabulary


## Schema

## CamelCase vs snake_case

Why we use snake case or underscores:

 * Easier to read, hence less chance of mistakes.
 * Consistent file system naming, regardless of case sensitivity.
 * Matches convention of naming database columns or properties (this is a storage format!)

## JSON-BL Document

A JSON-BL Document is the basic building block to be able to communicate using JSOB-BL. It is the first layer of a file or stream used to pass the JSON-BL object from one person or service to another. At the highest level, it is a simple JSON object consisting of two properties:

 * `header`, a JSON-BL Object of type `header` containing meta-data describing the information payload, and
 * `body`, a JSON-BL Object with a format that adheres to the body schema defined in the header.

A typical JSON-BL Document may look something like the following:

    {
      "$schema": "http://json-bl.org/draft-01/document#",
      "head": {
        "language": "JSON-BL",
        "version": "0.1.0",
        "generator": "Some App <application.com>",
        "body": {
          "schema": "http://json-blo.org/draft-01/documents/invoice#"
        },
        "authors": [
          {
            "alias": "sam",
            "name": "Samuel Lown",
            "company": "SamLown.com"
          }
        ],
        "history": {
          "created_at": "2012-11-23T12:34:00Z",
          "updated_at": "2012-11-23T12:34:00Z",
          "changes": [
            {
              "by_alias": "sam",
              "created_at": "2012-11-23T12:34:00Z",
              "version": "0.1.0",
              "description": "Initial version"
            }
          ]
        },
        "digest": {
          "algorithm": "SHA256",
          "value": "....."
        },

        "signature" {
          "algorithm": "X509-RSA-SHA1",
          "value": ".....",
          "x509_data": {
            "serial": {
              "issuer_name": "....",
              "serial_number": "....."
            }
          }
        }
      },
      "body": {
        // The of the document
      }
    }

### Header

The JSON-BL Document header is used to provide the reader, human or computer, relevant information about how to interpret the rest of the document.


### Security and Data Integrity

Unlike many other standards that address data security through patches or additional standards, data integrity and security are required features for any JSON-BL implementation.

#### Preparing the Document for Hashing

To be able to digitally sign or create a digest of a document, it is essential to have a consistent method of representing the same data in exactly the same way everywhere. Without this, a single out of place character will cause the document to be un-verifiable.

In the world of XML, canonicalisation is used to create a consistent copy of the original source document. It provides definition for dealing with code fragments and namespaces that result in a highly complicated process. This gets even worse when trying to canonicalise in memory as the standard requires that all white space and comments be protected.

In JSON-BL, rather than trying to canonicalise a JSON object, a nested key-value serialization approach is taken. Essentially, a recursive process goes through each object, ordering the keys and flattening the key-value pairs to form a long concatenated string. Take the following JSON sample of a JSON-BL contact:

    {
      "full_name": "Mr. Fred Flinstone",
      "name": {
        "given": "Fred",
        "surname": "Flinstone",
        "prefix": "Mr."
      },
      "birth_date": "1940-02-02",
      "address": [
        {
          "label": "home",
          "street": "345 Cave Stone Road",
          "locality": "Bedrock"
        },
        {
          "label": "office",
          "street": "1313 Cobblestone Way",
          "locality": "Bedrock"
        }
      ]
    }

The following string would be suitable for creating a digest or signature:

    "addresslabelhomelocalityBedrockstreet345 Cave Stone RoadlabelofficelocalityBedrockstreet1313 Cobblestone Waybirth_date1940-02-02full_nameMr. Fred FlinstonenamegivenFredprefixMr.surnameFlinstone"

Should you want to try generating a hash, the Base64 SHA256 digest is:

    wKirfO9lIlqZ70cLr1oknmW+axE1uEasT0YonjHm78U=

There is however a small problem with this approach when dealing with a complete JSON-BL Document; the digests and signatures are part of the header that needs to be hashed. JSON-BL gets around this by simply not including the `digest`, `signature`, or `encryption` objects from the header in the flattened string.

To summarize, in JSON-BL, a document is prepared for hashing by:

 * recursively ordering each object's keys,
 * flattening each objects key-value pair before concatenating them together in order,
 * ignoring any comments in the source file, and,
 * ignoring the properties 'digest', 'signature', and 'encryption' contained in the header.


#### Digests


#### Digital Signatures

In the XMLDSIG standard and SOAP, a digital signature is generated from a section of the document known as the `SignedInfo` block which includes the digest. The approach in JSON-BL is much simpler; the same hash used to generate the digest is also used to generate the signature. With this approach, the digest and signature can be checked independently of each other removing any uncertainty if the digest or signature do not match the expected result: a valid digest, but an invalid signature most probably means you have an invalid certificate.




#### Encryption



#### Encryption and Signing Together



## JSON-BL Object

A JSON-BL Object is a regular JSON object that conforms to a specific JSON Schema defined by the JSON-BL standard.

Objects primarily consist of properties of basic types defined by JSON:

 * `number` - integer or double precision floating-point number
 * `string` - Unicode text with backslash escaping
 * `boolean` - true or false
 * `array` - an ordered sequence of values or objects, comma-separated and enclosed in square brackets; the values do not need to be of the same type.
 * `object` - an unordered collection of key:value pairs with the ':' character separating the key and the value, comma-separated and enclosed in curly braces; the keys must be strings and should be distinct from each other.

In addition to the basic JSON types, JSON-BL adds the concept of "object type through property". This is to say, the property or key used to point to the object, tells us which schema is used to describe the contents. The following section on JSON-BL Schema describes object types.


### JSON-BL Object Schema





### Handling Date and Time

Given that JSON has no standard way of dealing with dates, times, and timestamps, JSON-BL provides its own simple convention on how to handle these property types using suffixes. The following suffixes should be used:

 * `_at` - a timestamp reflecting the current date and time in ISO-8512 format and always in UTC. For example "2012-12-01T12:32:12.546Z".
 * `_sate` - the year, month and day, for example: "2012-12-01". Typically to be used for human reference as it does not take into account the time zone.
 * `_time` - the hours, minutes, and seconds of a time, for example: "12:32:12" or "12:32". Again, mainly used for human reference.


### Schema Library

Available in the schemas directory.


