= JBL - JSON Business Language

Because JSON really does mean Universal.

WARNING! THIS IS AN EXPERIMENT AND IS NO WHERE NEAR DRAFT STATUS... YET!

== Introduction

JBL is a consensus based standard to create and communicate business documents in an easy to use and down to earth language. The key focus is simplicity and portability, but with advanced security definitions built into the language to deal with digital signatures, digests, and encryption.

The JBL standard consists of the following key components:

 * Objects, these are the building blocks for the actual information,
 * Documents, for sending the objects from one place to another,
 * Validators to ensure the Documents and Objects they contain are valid, and that all signatures and digests are correct, and,
 * Builders, used to construct a Document from a root object including helpers for validation and digest/signature generation.

JSON Schema is used to describe each type of JBL Object.

The standard is based on a number of different source, but in large part the very feature complete UBL standard by OASIS forms a basis for most object definitions.

== Vocabulary




== JBL Document

A JBL Document is the basic building block to be able to communicate using JBL. It is the first layer of a file o stream used to pass the JBL object from one person or service to another. At the highest level, it is a simple JSON object consisting of two properties:

 * `header`, a JBL Object of type `header` containing meta-data describing the real information paylaod, and
 * the object, a property whose name matches the type of JBL Object that forms the base of the document.

While the JBL standard encourages the use of known object types, the reality is that any payload may be included after the header.

A typical JBL Document may look something like the following:

    {
      "header": {
        "language": "JBL",
        "version": "0.1.0",
        "objectType": "invoice",
        "generator": "autofiscal.com",
        "authors": [
          {
            "alias": "sam",
            "name": "Samuel Lown",
            "company": "SamLown.com"
          }
        ],
        "history": {
          "createdAt": "2012-11-23T12:34:00Z",
          "updatedAt": "2012-11-23T12:34:00Z",
          "changes": [
            {
              "byAlias": "sam",
              "createdAt": "2012-11-23T12:34:00Z",
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
          "x509Data": {
            "serial": {
              "issuerName": "....",
              "serialNumber": "....."
            }
          }
        }
      },
      "invoice": {
        ....
      }
    }

=== Header

The JBL Document header is used to provide the reader, human or computer, to determine important information about the document.


=== Security and Data Integrety

Unlike many other standards that address data security through patches or additional standards, data integrety and security are required features for any JBL implementation.

==== Preparing the Document for Hashing

To be able to digitally sign or create a digest of a document, it is essential to have a consistent method of representing the same data in exactly the same way everywhere. Without this, a single bit out of place will cause the document to be un-verifiable.

In the world of XML, canonicalisation is used to create a consistent copy of the original source document. It provides special support for dealing with code fragments and namespaces that result in a highly complicated process. This gets even worse when trying to canonicalise in memory as the standard requieres that all white space and comments be protected.

In JBL, rather than trying to canonicalise a JSON object, a nested key-value serialization approach is taken. Essentially, a recursive process goes through each object, ordering the keys and flattening the key-value pairs to form a long concatenated string. Take the following JSON sample of a JBL contact:

    {
      "fullName": "Mr. Fred Flinstone",
      "name": {
        "given": "Fred",
        "surname": "Flinstone",
        "prefix": "Mr."
      },
      /* We can't do BC dates :-) */
      "birthDate": "1940-02-02",
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

The following string would be suitable for creating a digest or signature, you'll notice that the comments are missing:

    "addresslabelhomelocalityBedrockstreet345 Cave Stone RoadlabelofficelocalityBedrockstreet1313 Cobblestone WaybirthDate1940-02-02fullNameMr. Fred FlinstonenamegivenFredprefixMr.surnameFlinstone"

Should you want to try this, the Base64 SHA256 digest of this string is:

    wKirfO9lIlqZ70cLr1oknmW+axE1uEasT0YonjHm78U=

There is however a small problem with this approach when dealing with a complete JBL Document; the digests and signatures are part of the header that needs to be hashed. JBL gets around this by simply not including the `digest`, `signature`, or `encryption` objects from the header in the flattened string.

To summarize, in JBL, a document is prepared for hashing by:

 * recursively ordering each object's keys,
 * flattening each objects key-value pair before concatinating them together in order,
 * ignoring any comments in the source file, and,
 * ignoring special digest, signature, and encryption objects in the header.


==== Digests


==== Digital Signatures


==== Encryption



==== Encrption and Signing Together



== JBL Object

A JBL Object is a regular JSON object that conforms to a specific JSON Schema defined by the JBL standard.

Objects primarily consist of properties of basic types defined by JSON:

 * `number` - integer or double precision floating-point number
 * `string` - Unicode text with backslash escaping
 * `boolean` - true or false
 * `array` - an ordered sequence of values or objects, comma-separated and enclosed in square brackets; the values do not need to be of the same type.
 * `object` - an unordered collection of key:value pairs with the ':' character separating the key and the value, comma-separated and enclosed in curly braces; the keys must be strings and should be distinct from each other.

In addition to the basic JSON types, JBL adds the concept of "object type through property". This is to say, the property or key used to point to the object, tells us which schema is used to describe the contents. The following section on JBL Schema describes object types.


=== JBL Schema





=== Handling Date and Time

Given that JSON has no standard way of dealing with dates, times, and timestamps, JBL provides its own simple convention on how to handle these property types using suffixes. The following suffixes should be used:

 * `At` - a timestamp reflecting the current date and time in ISO-8512 format and always in UTC. For example "2012-12-01T12:32:12.546Z".
 * `Date` - the year, month and day, for example: "2012-12-01". Typically to be used for human reference as it does not take into account the time zone.
 * `Time` - the hours, minutes, and seconds of a time, for example: "12:32:12" or "12:32". Again, mainly used for human reference.


=== Schema Library
