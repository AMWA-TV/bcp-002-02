# \[Work In Progress\] AMWA BCP-002-02 NMOS Asset Distinguishing Information 
{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

## Scope

This Best Current Practice specifies how to provide additional metadata within NMOS APIs to enable a human to more easily distinguish among multiple NMOS Assets.

## Introduction

Within even the most modestly-sized system, there are likely to be multiple instances of a given product, and possibly multiple instances of different products provided by the same Manufacturer.
It is fairly common for a configuration engineer to have to carry out 'detective work' to determine which Asset – an NMOS Node or Device – is which. 

For example an IS-04 query might return multiple Nodes, all with a label of `Acme-Widget-Pro`.
Without any other knowledge, a configuration engineer has no idea whether these are, for example, CCUs or Multiviewers, or which "Acme Widget Pro" is which in the installation.

As the size of the installation grows, the problem increases significantly, and further confusion may occur as we introduce more products manufactured by Acme.

Prior to the creation of this Best Current Practice, there was no common, established way of adding Distinguishing Information to NMOS Assets.
Distinguishing Information is additional metadata to assist a configuration engineer when browsing/importing/allocating Assets.

This Best Current Practice specifies the use of four Tags defined in the [NMOS Parameter Registers][NPR] to provide Distinguishing Information for Node and Device resources in [IS-04][].

> Note: This does not prohibit the Manufacturer from also adding 'user defined' Tags (typical usage might be Location, Frame Identifier, etc.).

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

See also the [NMOS Glossary][Glossary].

### Asset

A Node or Device. A Node is a logical host for Devices. A Device is a logical block of functionality. These Devices can be physical or virtual, for example software modules or plugin hardware.

### Distinguishing Information

One or more items of metadata associated with an Asset that (in combination) provide a human-friendly way of identifying that Asset.

The Distinguishing Information will match what is typically physically written on a hardware Asset and/or provided by the Manufacturer through a user interface or API.

The following sub-sections define metadata items that are used as Distinguishing Information.

#### Manufacturer

The name used by the company or other organisation creating/selling an Asset, e.g. "Acme". Manufacturers are encouraged to normalise this across their range of products.

> Note: The AMWA does not maintain a registry of vendor names.

The Manufacturer of a Device need not be the same as the Node which hosts it; for example Acme might manufacture a hardware frame that provides the NMOS Node and is populated with third-party processing cards, or software modules, acting as NMOS Devices.

#### Product Name

The name given by the Manufacturer for the Asset, e.g. "Widget Pro". Again, Devices might be different products to their enclosing Node.

#### Instance Identifier

Further distinguishes Assets that have the same Product Name.

For a hardware Asset this is the serial number assigned by the Manufacturer, e.g. "XYZ123-456789".

For a virtual or containerised Asset this could be an identifier provided by the manufacturer or by dynamic provisioning.

#### Function

The name of the function implemented by a Device within its Node, for example "Decoder", "Encoder", "Converter" or "Analyzer".

## Tagging Distinguishing Information

Node implementations MUST indicate Distinguishing Information in the [IS-04][] Node API using `asset` tags as defined in the [Tags register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/) of the [NMOS Parameters Registers][NPR] as follows.

Node and Device resources MUST include exactly one value for each of the following tags:

- [Manufacturer](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-manufacturer)
- [Product Name](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-product)
- [Instance Identifier](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-instance-identifier)

The combination of Manufacturer, Product Name and Instance Identifier MUST be unique among Nodes or Devices.

> Note: A Device can have the same Manufacturer, Product Name and/or Instance Identifier as its associated Node.

Device resources MUST also include at least one value for the following tag:

- [Function](https://specs.amwa.tv/nmos-parameter-registers/branches/main/tags/#asset-function)

The tag values MUST reflect the current state of the Node or Device.

> Note: this means that if a Device changes its function it will have to update its Function tag value.

## Examples

1. Simple product, a Node with a single logical Device.

   Node API `/self` resource:
   ```json
   {
     ...
     "tags": {
       "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
       "urn:x-nmos:tag:asset:product/v1.0": [ "Model-A" ],
       "urn:x-nmos:tag:asset:instance-id/v1.0": [ "12345ABC" ]
     },
     ...
   }
   ```

   Node API `/devices` resource:
   ```json
   [
     {
      ...
       "tags": {
         "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
         "urn:x-nmos:tag:asset:product/v1.0": [ "Model-A" ],
         "urn:x-nmos:tag:asset:instance-id/v1.0": [ "12345ABC" ],
         "urn:x-nmos:tag:asset:function/v1.0": [ "Decoder" ]
       },
       ...
     }
   ]
   ```

2. Node with two identical Devices where each is a different product with a unique serial number.

   Node API `/self` resource:
   ```json
   {
     ...
     "tags": {
       "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
       "urn:x-nmos:tag:asset:product/v1.0": [ "Model-A" ],
       "urn:x-nmos:tag:asset:instance-id/v1.0": [ "FRAME-12345ABC" ]
     },
     ...
   }
   ```

   Node API `/devices` resource:
   ```json
   [
     {
       ...
       "tags": {
         "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
         "urn:x-nmos:tag:asset:product/v1.0": [ "Model-B" ],
         "urn:x-nmos:tag:asset:instance-id/v1.0": [ "DEC-12345ABC" ],
         "urn:x-nmos:tag:asset:function/v1.0": [ "Decoder" ]
       },
       ...
     },
     {
       ...
       "tags": {
         "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
         "urn:x-nmos:tag:asset:product/v1.0": [ "Model-C" ],
         "urn:x-nmos:tag:asset:instance-id/v1.0": [ "DEC-67890XYZ" ],
         "urn:x-nmos:tag:asset:function/v1.0": [ "Decoder" ]
       },
       ...
     }
   ]
   ```

3. Node with two identical Devices where only the Node has a unique serial number.

   Node API `/self` resource:
   ```json
   {
     ...
     "tags": {
       "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
       "urn:x-nmos:tag:asset:product/v1.0": [ "Model-A" ],
       "urn:x-nmos:tag:asset:instance-id/v1.0": [ "12345ABC" ]
     },
     ...
   }
   ```

   Node API `/devices` resource:
   ```json
   [
     {
       ...
       "tags": {
         "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
         "urn:x-nmos:tag:asset:product/v1.0": [ "Model-A" ],
         "urn:x-nmos:tag:asset:instance-id/v1.0": [ "12345ABC-1" ],
         "urn:x-nmos:tag:asset:function/v1.0": [ "Decoder" ]
       },
       ...
     },
     {
       ...
       "tags": {
         "urn:x-nmos:tag:asset:manufacturer/v1.0": [ "Vendor-A" ],
         "urn:x-nmos:tag:asset:product/v1.0": [ "Model-A" ],
         "urn:x-nmos:tag:asset:instance-id/v1.0": [ "12345ABC-2" ],
         "urn:x-nmos:tag:asset:function/v1.0": [ "Decoder" ]
       },
       ...
     }
   ]
   ```

## Controllers

Controllers MUST enable Users to see the Distinguishing Information associated with an Asset or its sub-resources.
Controllers SHOULD enable Users to search for an Asset or its sub-resources based on Distinguishing Information.

## Normative References

These appear at the end of the Markdown source for this document, and are referenced as hyperlinks within the main body.

[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs to Indicate Requirement Levels"
[Glossary]: https://specs.amwa.tv/nmos/main/docs/Glossary.html "NMOS Glossary"
[IS-04]: https://specs.amwa.tv/is-04 "AMWA IS-04 NMOS Discovery and Registration Specification (Stable)"
[NPR]: https://specs.amwa.tv/nmos-parameter-registers "NMOS Parameter Registers"
