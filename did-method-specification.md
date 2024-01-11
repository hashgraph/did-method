# Hedera Hashgraph DID Method Specification
Version 0.9, Hedera Hashgraph
 
## Table of Contents 
- [Hedera Hashgraph DID Method Specification](#hedera-hashgraph-did-method-specification)
  - [Table of Contents](#table-of-contents)
  - [About](#about)
  - [Abstract](#abstract)
  - [Status of This Document](#status-of-this-document)
  - [Motivation](#motivation)
  - [Hedera Hashgraph DID Method](#hedera-hashgraph-did-method)
    - [Namespace Specific Identifier (NSI)](#namespace-specific-identifier-nsi)
      - [Method-Specific DID URL Parameters](#method-specific-did-url-parameters)
        - [Appnet Address Book](#appnet-address-book)
  - [CRUD Operations](#crud-operations)
    - [Events](#events)
      - [DIDOwner](#didowner)
      - [VerificationMethod](#verificationmethod)
      - [Verification relationship](#verification-relationship)
      - [Services](#services)
    - [Operations](#operations)
      - [Create](#create)
      - [Read](#read)
      - [Update](#update)
      - [Revoke](#revoke)
      - [Delete](#delete)
  - [Security Considerations](#security-considerations)
  - [Privacy Considerations](#privacy-considerations)
  - [Reference Implementations](#reference-implementations)
  - [References](#references)

## About
This [DID method specification](https://w3c-ccg.github.io/did-spec/#specific-did-method-schemes) conforms to the requirements in the DID specification currently published by the W3C Credentials Community Group. For more information about DIDs and DID method specifications, please see the [DID Primer](https://github.com/WebOfTrustInfo/rwot5-boston/blob/master/topics-and-advance-readings/did-primer.md) and [DID Spec](https://w3c.github.io/did-core/).

The following DID Method will be registered in the [DID Method Registry](https://w3c-ccg.github.io/did-method-registry/).

## Abstract
Hedera Hashgraph is a multi-purpose open public ledger that uses hashgraph consensus - a fast, fair, and secure alternative to blockchains.

## Status of This Document
This document was published as an Editor's Draft.

Publication as an Editor's Draft does not imply endorsement by the W3C Membership. This is a draft document and may be updated, replaced or obsoleted by other documents at any time. It is inappropriate to cite this document as other than a work in progress.

## Motivation
This document defines a binding of the Decentralized Identifier architecture to Hedera Hashgraph - specifically how to use the Hedera File Service to record membership in 'business application networks' (appnets) and how to use the Hedera Consensus Service (HCS) for CRUD mechanisms on DID documents stored in such business application network. An business application network is a network of computers that store some set of business data (such as DID Documents) in a shared state, and rely on the Hedera mainnet for timestamping and ordering the transactions that cause that business application network state to change. An business application network could be exclusively dedicated to managing DID Documents and other identity artifacts in its state, or it could itself be multi-purpose. For instance, a Supply Chain could establish an business application network and, in addition to using HCS for tracking the location of shipments, use the DID method defined here to manage the identities of the associated companies, employees, and IoT devices. The HCS model is designed to off load from the Hedera mainnet node the burden of disk writes and long term storage and so allow those nodes to be optimized for high throughput ordering of transactions. 

## Hedera Hashgraph DID Method
The namestring that shall identify this DID method is: `hedera`

A DID that uses this method MUST begin with the following prefix: `did:hedera`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is the NSI specified below.

### Namespace Specific Identifier (NSI)
The `did:hedera` namestring is defined by the following ABNF:
```abnf
hedera-did = "did:hedera:" hedera-specific-idstring "_" hedera-specific-parameters
hedera-specific-idstring = hedera-network ":" hedera-base58-key
hedera-specific-parameters = did-topic-id
did-topic-id = 1*DIGIT "." 1*DIGIT "." 1*DIGIT

hedera-network = "mainnet" / "testnet"
hedera-base58-key = 32*44(base58)
base58 = "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9" / "A" / "B" /
         "C" / "D" / "E" / "F" / "G" / "H" / "J" / "K" / "L" / "M" / "N" /
         "P" / "Q" / "R" / "S" / "T" / "U" / "V" / "W" / "X" / "Y" / "Z" /
         "a" / "b" / "c" / "d" / "e" / "f" / "g" / "h" / "i" / "j" / "k" /
         "m" / "n" / "o" / "p" / "q" / "r" / "s" / "t" / "u" / "v" / "w" /
         "x" / "y" / "z"
```

Example:
```
did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345
```

The method specific identifier `hedera-specific-idstring` is composed of a Hedera network identifier with a `:` separator followed by a `hedera-base58-key` identifier which is a base58 Encoded of a DID root public key and a `did-topic-id` (see details below). 

Hedera DIDs are not required to be registered on the ledger and may be used as unregistered pseudonymous pairwise identifiers. However, these identifiers may also be registered on the ledger and be publicly resolvable.

Every DID document registered on Hedera network MUST contain a public key of id `#did-root-key` and type `Ed25519VerificationKey2018`. The `hedera-base58-key` identifier is a base58 Encoded of this public key.

Example Hedera DID document:
```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345",
  "authentication": [
    "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345#did-root-key"
  ],
  "VerificationMethod": [
    {
      "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345#did-root-key",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345",
      "publicKeyMultibase": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  ],
  "service": [
    {
      "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345#vcs",
      "type": "VerifiableCredentialService",
      "serviceEndpoint": "https://example.com/vc/"
    }
  ]
}
```

#### Method-Specific DID URL Parameters
There is one method-specific parameter defined for a Hedera DID:
- `did-topic-id` - an mandatory parameter that defines a TopicID of Hedera Consensus Service topic to which a particular DID document was submitted. This allow us to resolve DIDs publicy through Hedera mirror nodes.

A Hedera TopicID is a triplet of numbers, e.g. `0.0.21243` represents a topic identifier `21243` within realm `0` within shard `0`.

Realms allow Solidity smart contracts to run in parallel. Realms are not relevant to this DID Method. In the future, when the number of consensus nodes warrants, the Hedera network will be divided up into shards such that a particular transaction will be processed into consensus only by a subset of the full set of nodes.

## CRUD Operations
Hedera network nodes support a Consensus Service API by which clients submit transaction messages to a topic - these transactions assigned a consensus timestamp and order before flowing back out to mirror nodes and any business application network subscribed to the relevant topic. Each DID Owner must have a dedicated HCS topic created for DID documents registration. Relevant business application network will subscribe to the corresponding topics, and consesequently retrieve and store valid DID documents submitted to the topic. 

Create, Update and Delete operations against a DID Document are submitted via the Consensus Service API, either directly by a DID Owner.

The two alternatives are:
![alt text](./images/create-update-delete.flow.svg "Create, Update and Delete flow alternatives")

The Read operation happens against either a mirror node or a member of the relevant appnet for the DID in question.

The two alternatives are:
![alt text](./images/read.flow.svg "Read flow alternatives")

A valid Create, Update, Revoke or Delete message must have a JSON structure defined by a [DIDMessage-schema](DIDMessage.schema.json) and contains the following properties:
- `message` - The message content with the following attributes:
  - `operation` - DID method operation to be performed on the DID document.  Valid values are: `create` , `update` , `revoke` and `delete`.
  - `did` - a plain DID.
  - `event` - A Base64-encoded list of events in JSON notation that conforms the different properties of a DID documents to the [DID Specification](https://w3c.github.io/did-core/), the accepted events are listed within this specification
  - `timestamp` - A message creation time. The value MUST be a valid XML datetime value, as defined in section 3.3.7 of [W3C XML Schema Definition Language (XSD) 1.1 Part 2: Datatypes](https://www.w3.org/TR/xmlschema11-2/). This datetime value MUST be normalized to UTC 00:00, as indicated by the trailing "Z". It is important to note that this timestamp is a system timestamp as a variable part of the message and does not represent a consensus timestamp of a message submitted to the DID topic.
- `signature` - A Base64-encoded signature that is a result of signing a minified JSON string of a message attribute with a private key corresponding to the public key `#did-root-key` in the DID document.

Neither the Hedera network nor mirror nodes validate the DID Documents against the above requirements - it is business application network members that, as part of their subscription logic, must validate DID Documents based on the above criteria. The messages with duplicate signatures shall be disregarded and only the first one with verified signature is valid (in consensus timestamp order).

Here is an example of a complete message wrapped in an envelope and signed:
```json
{
  "message": {
    "operation": "update",
    "did": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm__0.0.12345", 
    "event": "ewogICJAY29udGV...9tL3ZjLyIKICAgIH0KICBdCn0=",
    "timestamp": "2020-04-23T14:37:43.511Z"
  },
  "signature":  "QNB13Y7Q9...1tzjn4w=="
}
```

It is a responsibility of an business application network's administrators to decide who can submit messages to their DID topic. Access control of message submission is defined by a `submitKey` property of `ConsensusCreateTopicTransaction` body. If no `submitKey` is defined for a topic, then any party can submit messages against the topic. Detailed information on Hedera Consensus Service APIs can be found in the official [Hedera API documentation](https://docs.hedera.com/hedera-api/consensus/consensusservice).

### Events

#### DIDOwner

Each identifier always has a controller address. By default, it is the same as the identifier address, however the resolver must validate this against the DID document. This controller address must be represented in the DID document as a verificationMethod entry with the id set as the DID being resolved and with the fragment `#did-root-key` appended to it. A reference to it must also be added to the authentication and assertionMethod arrays of the DID document.

If the controller for a particular DID is changed, a DIDOwner event is emitted through an DID update Message.
The event data MUST be used to update the `#did-root-key` entry in the verificationMethod array.

DIDOwner event must have a JSON structure defined by a [DIDOwner-schema](DIDOwner.schema.json) and contains the following properties:
- `DIDOwner` - The DIDOwner event with the following attributes:
  - `id` - The Id property of the verification method.
  - `type` - reference to the verification method type.
  - `controller` - The DID of the entity that is authorized to make the change.
  - `publickeyMultibase` - Mutlibase encoded public key.

```json
{
  "DIDOwner": {
    "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm__0.0.12345",
    "type": "Ed25519VerificationKey2018", 
    "controller": "did:hedera:mainnet:a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
    "publicKeyMultibase": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV",
  },
}
```

#### VerificationMethod

The DID specification offers the possiblity to add multiple verfication methods associated with a DID via a DID Documents. These verifications methods can represent:
* A parent who controls a child's DID document might permit the child to use their personal device in order to authenticate (DID Delegate).
* Linking certain verification methods to specific verification purposes.

VerificationMethod event must have a JSON structure defined by a [VerificationMethod-schema](VerificationMethod.schema.json) and contains the following properties:
- `VerificationMethod` - The VerificationMethod event with the following attributes:
  - `id` - The Id property of the verification method.
  - `type` - reference to the verification method type.
  - `controller` - The DID of the entity that is authorized to make the change.
  - `publickeyMultibase` - Mutlibase encoded public key.

```json
{
  "VerificationMethod": {
    "id":"did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345#delegate-key1",
    "type": "Ed25519VerificationKey2018", 
    "controller": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm__0.0.12345",
    "publicKeyMultibase": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV",
  },
}
```

#### Verification relationship

The verification relationship event enable the associated verification methods to be used for different purposes.he The event offers support for :
* Authentication verification relationship is used to specify how the DID subject is expected to be authenticated, for purposes such as logging into a website or engaging in any sort of challenge-response protocol. 
* Assertion verification relationship allows verifier to check if a verifiable credential contains a proof created by the DID subject.
* The keyAgreement verification relationship allows the DID subject to specify how an entity can generate encryption material in order to transmit confidential information, such as for the purposes of establishing a secure communication channel with the recipient. 
* Capability Invocation relationship allows the DID subject to invoke a cryptographic capability for a specific authorisation.
* Capability Delegation relationship is used to specify a mechanism that might be used by the DID subject to delegate a cryptographic capability to another party, such as delegating the authority to access a specific HTTP API to a subordinate. 

VerificationRelationship event must have a JSON structure defined by a [VerificationRelationship-schema](VerificationRelationship.schema.json) and contains the following properties:
- `VerificationRelationship` - The VerificationRelationship event with the following attributes:
  - `id` - The Id property of the verification method.
   - `relationshipType` - Relationship type that is linked to specific verification method. to be performed on the DID document.  Valid values are: `authentication` , `assertionMethod` , `keyAgreement` , `capabilityInvocation` and `capabilityDelegation`.
  - `type` - reference to the verification method type.
  - `controller` - The DID of the entity that is authorized to make the change.
  - `publickeyMultibase` - Mutlibase encoded public key.

```json
{
  "VerificationRelationship": {
    "id":"did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345#delegate-key1",
    "relationshipType": "authentication",
    "type": "Ed25519VerificationKey2018", 
    "controller": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm__0.0.12345",
    "publicKeyMultibase": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV",
  },
}
```

#### Services

Services are used in DID documents to express ways of communicating with the DID subject or associated entities. A service can be any type of service the DID subject wants to advertise, including decentralized identity management services for further discovery, authentication, authorization, or interaction. 

Service event must have a JSON structure defined by a [service-schema](Service.schema.json) and contains the following properties:
- `Service` - The Service event with the following attributes:
  - `id` - The Id property of the service.
  - `type` - reference to the service type.
  - `serviceEndpoint` - valid urn to the service endpoint.



```json
{
  "Service":
    {
      "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm_0.0.12345#vcs",
      "type": "VerifiableCredentialService",
      "serviceEndpoint": "https://example.com/vc/"
    },
}
```


### Operations

#### Create
A DID Document is created implicit, by having hedera account, or via a transaction within a particular business application network by sending a `ConsensusSubmitMessage` transaction to a Hedera network node. 
It is executed by sending a `submitMessage` RPC call to the HCS API with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of appnet's DID topic
- `message` - a JSON DID message envelope described above with `operation` set to `create`

Business application network members subscribed to this DID topic shall store the DID document in their local storage upon receiving this message from a mirror.

#### Read
Read, or DID resolution, does not occur via HCS messages but rather directly against a computer that has persisted the DID Document.

How resolution of a DID into the corresponding DID Document occurs depends on whether the DID Document was submitted in encrypted or plaintext mode and, where resolution happens.

If submitted in plaintext mode, then resolution can occur either against a mirror node that may have persisted the history of messages for that DID Document, or against a member of the business application network that persisted the DID Document.

If resolved against a mirror, resolution requires reading the latest transaction message submitted to a DID topic for the given DID and checking if there was no earlier transaction for this DID with `operation` set to `delete` or another message with the same `signature`. This method can only be executed on a mirror node and may require reading a full history of messages in the topic. Hedera mirror nodes provide a [Hedera Consensus Service gRPC API](https://docs.hedera.com/guides/docs/mirror-node-api/hedera-consensus-service-api-1). Client applications can use this API to subscribe to an appnet's DID topic to receive DID messages submitted to it and filter those that contain the DID to be resolved. 

Here is an example Java client, please refer to the official Hedera documentation for more information:
```java
new MirrorConsensusTopicQuery()
    .setTopicId(didTopicId)
    .subscribe(mirrorClient, resp -> {
          String didMessage = new String(resp.message, StandardCharsets.UTF_8);
          System.out.println(resp.consensusTimestamp + " received DID message: " + didMessage);
      },
      // On gRPC error, print the stack trace
      Throwable::printStackTrace);
    });
```

If resolved against an business application network member, resolution requires calling business application network relay service (defined below). 

If the DID Document were submitted in encrypted mode, then resolution against a mirror is not possible unless the verifier is in possession of a decryption key and the business application network service is the only way of resolution.


#### Update
A property or a DID document is updated by sending a `ConsensusSubmitMessage` transaction to a Hedera network node. It is executed by sending a `submitMessage` RPC call to HCS with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of the appropriate appnet's DID topic
- `message` - a JSON DID message envelope described above with `operation` set to `update`

Business application network members subscribed to this DID topic shall replace the previous version of the DID document from their storage with this new version upon receiving this message from a mirror.


#### Revoke 
A property or a DID document is revoked by sending `ConsensusSubmitMessage` transaction to a Hedera network node. It is executed by sending a `submitMessage` RPC call to HCS with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of appnet's DID topic
- `message` - a JSON DID message envelope described above with `operation` set to `revoke` 
Business application network members subscribed to this DID topic shall delete the DID document from their storage or mark it as deleted upon receiving this message from a mirror.



#### Delete 
A Whole DID document is deleted/nullified by sending `ConsensusSubmitMessage` transaction to a Hedera network node. It is executed by sending a `submitMessage` RPC call to HCS with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of appnet's DID topic
- `message` - a JSON DID message envelope described above with `operation` set to `delete`


## Security Considerations

Security of Hedera DID Documents inherits the security properties of Hedera Hashgraph network itself.

Hedera Hashgraph uses the hashgraph algorithm for the consensus timestamping and ordering of transactions. Hashgraph is Asynchronous Byzantine Fault Tolerant (ABFT) and fair, in that no particular node has the sole authority to decide the order of transactions, even if only for a short period of time. 

Hedera uses a proof of stake (POS) model to mitigate Sybil attacks. The influence of a particular node towards consensus is weighted by the amount of hbars, the network's native coin, they control.

Hedera charges fees for the processing of transactions into consensus and to partially mitigate Denial of Service attacks.

In the HCS model, messages are submitted to the Hedera network nodes, which collectively assign them a consensus timestamp and order within a topic, and then are deleted from those network consensus nodes. Consensus network nodes do not persist HCS messages beyond 3 minutes.

The messages are persisted only on mirror nodes, and appnet members that are subscribed to the corresponding topic.

Mirrors may persist HCS messages and are presumed to be public. Consequently, any data sent via HCS messages should be encrypted if sensitive. 

A public DID Document is sent unencrypted. Public DIDs/DID Documents include public keys and service endpoints

Write access to Hedera Consensus Service DID Topics can be controlled by stipulating a list of public keys for the topic by business application network administrators. Only HCS messages signed by the corresponding private keys will be accepted. A key can be a "threshold key", which means a list of M keys, any N of which must sign in order for the threshold signature to be considered valid. The keys within a threshold signature may themselves be threshold signatures, to allow complex signature requirements. 


## Privacy Considerations

Regardless of encryption, a DID Document should not include Personally Identifiable Information (PII). 

The identifiers used to identify a subject create a greater risk of correlation when those identifiers are long-lived or used across more than one application domain as those domains could use that shared handle for the subject to share information about that subject without their express consent.

If DID Controllers want to mitigate the risk of correlation, they should use unique DIDs for every interaction and the corresponding DID Documents should contain a unique public key. 

The resolution process may leak PII as the resolver can infer that the Subject presenting the DID is interacting with the verifier resolving the DID. 

## Reference Implementations
The code at [https://github.com/hashgraph/did-sdk-java](https://github.com/hashgraph/did-sdk-java) is intended to provide a Java SDK for this DID method specification. A set of unit tests and example appnet application within this repository present a reference implementation of this DID method.

## References
* <https://w3c-ccg.github.io/did-spec/>
* <https://github.com/hashgraph/did-sdk-java> 
* <https://docs.hedera.com/hedera-api/>
* <https://www.hedera.com/>
