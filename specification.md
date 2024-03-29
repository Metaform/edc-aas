# Catena-X Technical Specification for Applying DSP Usage Control to AAS data

## 1. C-X JSON-LD Namespace

The namespace prefix 'cx' must resolve to `--- insert Catena-X namespace -----`. When an AAS request is made, an HTTP header set to the `authKey` with a value of `authCode`must be
present.

## 2. DSP Catalog

The DSP catalog provides discovery for DSP assets (datasets) published by a provider in a dataspace. This specification defines a DCAT property extension to make AAS Registries
discoverable from and DSP catalog. It also defines two dataset

### 3.1 AAS Registry entry

To link to an AAS registry, an DSP dataset may include a `cx:aasRegistry` property of type `xsd:anyURI`:

```
  "dcat:dataset": [
    {
      "@id": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
      "@type": "dcat:Dataset",
      "dct:title": "AAS Asets",
      "dct:description": "Asset Administrtion Shells",
      "odrl:hasPolicy": {...}
      "cx:aasRegistry": "https://provider.com/aas",
     ...
    }
  ]
```

### 3.2 AAS types

This specification defines AAS interface types using the `cx:aasInterface` property:

```
  "dcat:dataset": [
    {
      "@id": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
      "@type": "dcat:Dataset",
      "dct:title": "AAS Asets",
      "dct:description": "Asset Administrtion Shells",
      "odrl:hasPolicy": {...}
      "cx:aasRegistry": "https://provider.com/aas",
      "cx:aasInterface": "AAS-REGISTRY",
     ...
    }
  ]  
```

The valid values for the `aasInterface` property are those defined by "interface" type in the _**Details of the Asset Administration Shell, Part 2**_ specification. 

## 4. The DSP Endpoint and `protocolInformation` type

The association between DSP datasets and AAS assets will be made available in an AAS Registry using DSP-specific `protocolInformation` entry for an endpoint in an asset
or submodel descriptor. With this method, it is possible to support either of the two modelling approaches specified in the previous section. In the case where the association is
1..N, the endpoint and `protocolInformation` entry can be included in the POSTed AAS descriptor, or the AAS Registry may include an implementation-specific mechanism for
defining endpoint and `protocolInformation` entries for a category of AAS types.

The following is an example of an endpoint entry with DSP `protocolInformation`:

```json
{
  "protocolInformation": {
    "endpointAddress": "https://test.com/shells/{aasIdentifier}/submodels/{submodelIdentifier}/submodel",
    "endpointProtocol": "AAS/DSP",
    "endpointProtocolVersion": "0.8",
    "subprotocol": "Dataspace Protocol Specification 0.8",
    "subprotocolBody": "catalogEndpoint=https://test.com/catalog;dataset=79afc338-f7ea-4255-a17d-ba6faf40d2b5",
    "subprotocolBodyEncoding": "plain"
  },
  "interface": "SUBMODEL-1.0"
}
```

| Attribute               | Type   | Description                                                                                                                                      |
|-------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| endpointAddress         | URL    | The URL must point to an endpoint that implements the AAS API. Currently, only the HTTPS binding is supported.                                   |
| endpointProtocolVersion | string | The AAS/DSP protocol version supported by the endpoint.                                                                                          |
| subprotocol             | string | The AAS/DSP protocol description.                                                                                                                |
| subprotocolBody         | string | A string in the form `catalogEndpoint=;dataset=`, which must contain the catalog endpoint URL and the DSP dataset id the AAS asset is mapped to. |
| subprotocolBodyEncoding | string | Currently, only `plain` is supported.                                                                                                            |
| interface               | string | The protocol and version, currently `SUBMODEL-1.0`.                                                                                              |

> The `subprotocolBody` attribute is used to specify the DSP catalog endpoint and dataset id for expediency. Our intent is to ask the relevant AAS working group to provide a
> facility for expressing custom attributes and use that to define two new attributes for this data.

## 5. DSP DataAddress

The Dataspace Protocol defines a `DataAddress ` type as part of the `TransferStartMessage`. The contents of the `DataAddress` are left open by DSP. This specification defines
the `DataAddress` to be a JSON object type encoded as JSON-LD with the following properties:

| Key          | Value                                     |
|--------------|-------------------------------------------|
| cx:authKey  | The authorization HTTP header name to use |
| cx:authCode | The authorization token                   |

## 6. Authorization

### 6.1 Token validation by Provider

The provider systems processing AAS requests are responsible for validating authorization tokens sent as headers in AAS requests (deriving from the DSP `DataAddress` sent in
the `TransferStartMessage`).

### 6.2 RBAC Security

The provider can implement granular security as part of each AAS asset request. This could be performed by a provider `Data Plane` via its `PolicyEngine` or by a backend system
that serves AAS assets.






