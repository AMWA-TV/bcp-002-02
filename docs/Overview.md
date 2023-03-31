# \[Work In Progress\] AMWA BCP-002-02 NMOS Asset Distinguishing Information 
{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

## Scope

This Best Current Practice specifies how to provide additional metadata within NMOS APIs to enable a human to more easily distinguish among multiple NMOS Assets.

## Introduction

Within even the most modestly-sized system, there are likely to be multiple instances of a given Product, and possibly multiple instances of different Products provided by the same Manufacturer.
It is fairly common for a configuration engineer to have to carry out 'detective work' to determine which Asset – an NMOS Node or Device – is which. 

For example an IS-04 query might return multiple Nodes, all with a label of `Acme-Widget-Pro`.
Without any other knowledge, a configuration engineer has no idea whether these are, for example, CCUs or Multiviewers, or which "Acme Widget Pro" is which in the installation.

As the size of the installation grows, the problem increases significantly, and further confusion may occur as we introduce more Products manufactured by Acme.

Prior to the creation of this Best Current Practice, there was no common, established way of adding Distinguishing Information to NMOS Assets.
Distinguishing Information is additional metadata to assist configuration engineer when browsing/importing/allocating Assets.

This Best Current Practice recommends the use of Tags, defined in the [NMOS Parameter Registers][NPR] to provide Distinguishing Information.

> Note: This does not prohibit the Manufacturer from also adding 'user defined' Tags (typical usage might be Location, Frame Identifier, etc.).

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

_See also the NMOS Glossary, and definitions within RFCs._

### Asset

A Node or Device. A Device is a logical block of functionality. A Node is a logical host for Devices. These Devices can be physical or virtual, for example software modules or plugin hardware.

### Distinguishing Information

One or more items of metadata associated with an Asset that (in combination) provide a human-friendly way of identifying that Asset.

The following sub-sections define metadata items that are used as Distinguishing Information.

#### Manufacturer

The name used by the company or other organisation creating/selling an Asset, e.g. "Acme". Manufacturers are encouraged to normalise this across their range of Products.

> Note: AMWA does not maintain a registry of vendor names.

The Manufacturer of a Device need not be the same as the Node which hosts it; for example Acme might manufacturer a hardware frame that provides the NMOS Node and is populated with third-party processing cards, or software modules, acting as NMOS Devices.

#### Product Name

The name given by the Manufacturer for the Asset, e.g. "Widget Pro". Again, Devices might be different products to their enclosing Node.

#### Instance Identifier

Further distinguishes Assets that have the same Product name.

For a hardware Asset this is the serial number assigned by the Manufacturer, e.g. "XYZ123-456789".

For a virtual or containerised Asset this could be an identifier provided by the manufacturer or by dynamic provisioning.

#### Function

The name of the function implemented by a Device within its Node, for example "UHD Decoder 01".

> TODO: add more examples

## Tagging Distinguishing Information

Node implementations MUST indicate Distinguishing Information using `asset` tags as defined in the [Tags register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/) of the [NMOS Parameters Registers][NPR] as follows.

Node and Device Resources MUST include exactly one value for each of the following tags:

- [Manufacturer](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-manufacturer)
- [Product Name](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-product)
- [Instance Identifier](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-instance-identifier)

The combination of Manufacturer, Product and Instance Identifier MUST be unique among Nodes or Devices.

> Note: A Device can have the same Manufacturer, Product and/or Instance Identifier as its associated Node.

Device Resources MUST also include exactly one value for the following tag:

- [Function](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-function)

The tag values MUST reflect the current state of the Node or Device.

> Note: this means that if a Device changes its function it will have to update its Function tag value.

## Example Structure

This shows example tags for a hardware device:

```json
{
 ...
  "tags": {
    "urn:x-nmos:tag:asset:manufacturer/v1.0": [
      "Acme"
    ],
    "urn:x-nmos:tag:asset:product/v1.0": [
      "Widget Pro"
    ],
    "urn:x-nmos:tag:asset:instance-id/v1.0": [
      "XYZ123-456789"
    ],
    "urn:x-nmos:tag:asset:function/v1.0": [
      "UHD Decoder"
    ]
  },
  ...
}
```

> TODO: add a virtualised/containerised example

## Normative References

These appear at the end of the Markdown source for this document, and are referenced as hyperlinks within the main body.

[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs to Indicate Requirement Levels"

[NPR]: https://specs.amwa.tv/nmos-parameter-registers "NMOS Parameter Registers"
