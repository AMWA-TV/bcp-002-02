# \[Work In Progress\] AMWA BCP-002-02 Distinguishing Information for NMOS Node and Device Resources
{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

## Scope

This Best Current Practice specifies how to provide additional metadata within NMOS APIs for an NMOS Node and an NMOS Device to enable a human to more easily distinguish among multiple Nodes and among multiple Devices.

## Introduction (informative)

Within even the most modestly-sized system, there are likely to be multiple instances of a given Product, and possibly multiple instances of different Products provided by the same Manufacturer.
It is fairly common for a configuration engineer to have to carry out 'detective work' to determine which Node (or Device) is which.

For example an IS-04 query might return multiple Nodes, all with a label of `Acme-Widget-Pro`. Without any other knowledge, a configuration engineer has no idea whether these are, for example, CCUs or Multiviewers, or which "Acme Widget Pro" is which in the installation.

As the size of the installation grows, the problem increases significantly, and further confusion may occur as we introduce more Products manufactured by Acme.

Prior to the creation of this Best Current Practice, there was no defined way of adding Distinguishing Information to NMOS Resources. Distinguishing Information is additional metadata to assist configuration engineer when browsing/importing/allocating Resources.  

This Best Current Practice recommends the use of Tags, defined in the [NMOS Parameter Registers][NPR] to provide Distguishing Information.

For IS-04 Node Resources this includes:

- Manufacturer
- Product
- Instance

For IS-04 Device Resources this includes:

- Manufacturer
- Product
- Application
- Instance

> Note: This does not prohibit the Manufacturer from adding 'user defined' Tags (typical usage might be Location, Frame Id etc).

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

_See also the NMOS Glossary, and definitions within RFCs._

### Distinguishing Information

One or more items of metadata associated with an NMOS Resource that (in combination) provide a human-friendly way of identifying that resource.

The following sub-sections define metadata items that can be used for Distinguishing Information:

#### Manufacturer

The name used by the company or other organisation creating/selling an NMOS Node or Device, e.g. "Acme". Manufacturers are encouraged to normalise this across their range of Products.

> Note: AMWA does not maintain a registry of vendor names.

The Manufacturer of an Device need not be the same as its Node; for example Acme might manufacturer a hardware frame that provides the NMOS Node and is populated with third-party processing cards, or software modules, acting as NMOS Devices.

#### Product

The name given by the Manufacturer for the NMOS Node or Device, e.g. "Widget Pro". Again, Devices might be different products to their enclosing Node.

#### Instance

Further identifies Nodes and Devices that have the same Product name.

For a hardware Node or Device this is the serial number assigned by the Manufacturer, e.g. "XYZ123-456789".

For a virtual or containerised Node or Device this could be an identifier provided by manufacturer or by dynamic provisioning.

#### Application

The name of the function implemented by a Device, for example "UHD Decoder 01".

## Tagging Distinguishing Information (normative)

Node implementations MUST indicate Distinguishing Information using `asset` tags as defined in the [NMOS Parameters Registers][NPR-TAGS-ASSET] as follows:

Node Resources MUST include exactly one such value for each of the following tags:

- Manufacturer
- Product
- Instance

The combination of Manufacturer, Product and Serial Number MUST be unique withing the IS-04 discovery context.

> TODO: define "discovery context or propose a different term".

Device Resources MUST include exactly one such value for each of the following tags:

- Manufacturer
- Product
- Application
- Instance

> TODO: add diagram

The combination of Manufacturer, Product, Application and Instance MUST be unique withing the IS-04 discovery context.

> Note: A Device can have the same Manufacturer, Product and/or Serial Number as its associated Node.

The tag values MUST reflect the current state of the Node or Device.

> Note: this means that if a Device changes its function it will have to update its Application tag value.

## Example structure (informative)

This shows example tags for a hardware device:

```json
{
 ...
  "tags": {
    "urn:x-nmos:tag:asset:facts:manufacturer/v1.0": [
      "Acme"
    ],
    "urn:x-nmos:tag:asset:facts:product/v1.0": [
      "Widget Pro"
    ],
    "urn:x-nmos:tag:asset:facts:application/v1.0": [
      "UHD Decoder"
    ],
    "urn:x-nmos:tag:asset:facts:instance/v1.0": [
      "XYZ123-456789"
    ]
  },
  ...
}
```

> TODO: add a virtualised/containerised example

## Normative References

These appear at the end of the Markdown source for this document, and are referenced as hyperlinks within the main body.

[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs to Indicate Requirement Levels"

[NPR-TAGS-ASSET]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/asset.html "Asset Tags"

[NPR]: https://specs.amwa.tv/nmos-parameter-registers "NMOS Parameter Registers"
