# Catena-X Technical Specification for Applying IDS Usage Control to AAS data

## 1. C-X JSON-LD Namespace

The namespace prefix 'c-x' must resolve to `--- insert Catena-X namespace -----`. When an AAS request is made, an HTTP header set to the `authKey` with a value of `authCode`must be
present.

## 2. IDS Catalog

The IDS catalog provides discovery for IDS assets (datasets) published by a provider in a dataspace. This specification defines a DCAT property extension to make AAS Registries
discoverable from and IDS catalog. It also defines two dataset

### 3.1 AAS Registry entry

To link to an AAS registry, an IDS dataset may include an `c-x:aasRegistry` property of type `xsd:anyURI`:

```
  "dcat:dataset": [
    {
      "@id": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
      "@type": "dcat:Dataset",
      "dct:title": "AAS Asets",
      "dct:description": "Asset Administrtion Shells",
      "odrl:hasPolicy": {...}
      "c-x:aasRegistry": "https:provider.com/aas",
     ...
    }
```

### 3.2 AAS types

This specification defines AAS interface types using the `c-x:aasInterfaceType` property:

```
  "dcat:dataset": [
    {
      "@id": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
      "@type": "dcat:Dataset",
      "dct:title": "AAS Asets",
      "dct:description": "Asset Administrtion Shells",
      "odrl:hasPolicy": {...}
      "c-x:aasRegistry": "https:provider.com/aas",
      "c-x:aasInterface": "AAS-REGISTRY",
     ...
    }
```

The valid values for the `aasInterface` property are those defined by "interface" type in the _**Details of the Asset Administration Shell, Part 2**_ specification. 

## 4. The IDS Endpoint and `protocolInformation` type

The association between IDS datasets and AAS assets will be made available in an AAS Registry using IDS-specific `protocolInformation` entry for an endpoint in an asset
or submodel descriptor. With this method, it is possible to support either of the two modelling approaches specified in the previous section. In the case where the association is
1..N, the endpoint and `protocolInformation` entry can be included in the POSTed AAS descriptor, or the AAS Registry may include an implementation-specific mechanism for
defining endpoint and `protocolInformation` entries for a category of AAS types.

The following is an example of an endpoint entry with IDS `protocolInformation`:

```json
{
  "protocolInformation": {
    "endpointAddress": "https://test.com/shells/{aasIdentifier}/submodels/{submodelIdentifier}/submodel",
    "endpointProtocol": "AAS/IDS",
    "endpointProtocolVersion": "0.8",
    "subprotocol": "Dataspace Protocol Specification 0.8",
    "subprotocolBody": "catalogEndpoint=https://test.com/catalog;dataset=79afc338-f7ea-4255-a17d-ba6faf40d2b5",
    "subprotocolBodyEncoding": "json-ld"
  },
  "interface": "AAS-1.0"
}
```

| Attribute               | Type   | Description                                                                                                                                      |
|-------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| endpointAddress         | URL    | The URL must point to an endpoint that implements the AAS API. Currently, only the HTTPS binding is supported.                                   |
| endpointProtocolVersion | string | The AAS/IDS protocol version supported by the endpoint.                                                                                          |
| subprotocol             | string | The AAS/IDS protocol description.                                                                                                                |
| subprotocolBody         | string | A string in the form `catalogEndpoint=;dataset=`, which must contain the catalog endpoint URL and the IDS dataset id the AAS asset is mapped to. |
| subprotocolBodyEncoding | string | Currently, only `json-ld` is supported.                                                                                                          |
| interface               | string | The protocol and version, currently `AAS-1.0`.                                                                                                   |

> The `subprotocolBody` attribute is used to specify the IDS catalog endpoint and dataset id for expediency. Our intent is to ask the relevant AAS working group to provide a
> facility for expressing custom attributes and use that to define two new attributes for this data.

## 5. IDS DataAddress

The IDS Dataspace Protocol defines a `DataAddress ` type as part of the `TransferStartMessage`. The contents of the `DataAddress` are left open by IDS. This specification defines
the `DataAddress` to be a JSON object type encoded as JSON-LD with the following properties:

| Key          | Value                                     |
|--------------|-------------------------------------------|
| c-x:authKey  | The authorization HTTP header name to use |
| c-x:authCode | The authorization header token            |

## 6. Authorization

### 6.1 Token validation by Provider

The provider systems processing AAS requests is responsible for validating authorization tokens sent as headers in AAS requests (deriving from the IDS `DataAddress` sent in
the `TransferStartMessage`).

### 6.2 RBAC Security

The provider can implement granular security as part of each AAS asset request. This could be performed by a provider `Data Plane` via its `PolicyEngine` or by a backend system
that serves AAS assets.






