# Hedera Hashgraph DID Method Specification
Version 0.1, Swisscom Blockchain AG
 
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
    - [Create](#create)
    - [Read](#read)
    - [Update](#update)
    - [Delete (Revoke)](#delete-revoke)
  - [Security Considerations](#security-considerations)
  - [Privacy Considerations](#privacy-considerations)
  - [Reference Implementations](#reference-implementations)
  - [References](#references)

## About
This [DID method specification](https://w3c-ccg.github.io/did-spec/#specific-did-method-schemes) conforms to the requirements in the DID specification currently published by the W3C Credentials Community Group. For more information about DIDs and DID method specifications, please see the [DID Primer](https://github.com/WebOfTrustInfo/rwot5-boston/blob/master/topics-and-advance-readings/did-primer.md) and [DID Spec](https://w3c.github.io/did-core/).

The following DID Method will be registered in the [DID Method Registry](https://w3c-ccg.github.io/did-method-registry/).

## Abstract
Hedera Hashgraph is a multi-purpose permissionless public ledger that uses hashgraph consensus, a faster, more secure alternative to blockchain consensus mechanisms.

## Status of This Document
This document was published as an Editor's Draft.

Publication as an Editor's Draft does not imply endorsement by the W3C Membership. This is a draft document and may be updated, replaced or obsoleted by other documents at any time. It is inappropriate to cite this document as other than a work in progress.

## Motivation
The general objective is to bind the Decentralized Identifier architecture to Hedera Hashgraph leveraging the native functionality of Hedera Consensus Service for appropriate mechanisms of DID documents and Hedera File Service to manage networks of trust defined by custom appnet implementations.

## Hedera Hashgraph DID Method
The namestring that shall identify this DID method is: `hedera`

A DID that uses this method MUST begin with the following prefix: `did:hedera`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is the NSI specified below.

### Namespace Specific Identifier (NSI)
The `did:hedera` namestring is defined by the following ABNF:
```abnf
hedera-did = "did:hedera:" hedera-specific-idstring ";" hedera-specific-parameters
hedera-specific-idstring = hedera-network ":" hedera-base58-key
hedera-specific-parameters = appnet-address-book [";" appnet-did-topic]
appnet-address-book = "hedera:" hedera-network ":fid=" appnet-address-book-file-id

appnet-did-topic = "hedera:" hedera-network ":tid=" appnet-did-topic-id
appnet-address-book-file-id = 1*DIGIT "." 1*DIGIT "." 1*DIGIT
appnet-did-topic-id = 1*DIGIT "." 1*DIGIT "." 1*DIGIT

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
did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123
```

The method specific identifier `hedera-specific-idstring` is composed of a Hedera network identifier with a `:` separator followed by a `hedera-base58-key` identifier which is a base58-encoded SHA-256 hash of a DID root public key (see details below). 

Hedera DIDs are not required to be registered on the ledger and may be used as unregistered pseudonymous pairwise identifiers. However, these identifiers may also be registered on the ledger within a specific appnet and be publicly resolvable or with access restriction defined by appnet owners. 

Every DID document registered on Hedera network MUST contain a public key of id `#did-root-key` and type `Ed25519VerificationKey2018`. The `hedera-base58-key` identifier is a base58-encoded SHA-256 hash of this public key.

Example Hedera DID document:
```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123",
  "authentication": [
    "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123#did-root-key"
  ],
  "publicKey": [
    {
      "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123#did-root-key",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123",
      "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  ],
  "service": [
    {
      "id": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123#vcs",
      "type": "VerifiableCredentialService",
      "serviceEndpoint": "https://example.com/vc/"
    }
  ]
}
```

#### Method-Specific DID URL Parameters
There are two method-specific parameters defined in Hedera DID:
- `fid` - a mandatory parameter that defines an ID of an appnet's address book file. 
- `tid` - an optional parameter that defines an ID of Hedera Consensus Service topic to which DID document was posted. This can be used in case of open and publicly available DID documents to resolve DIDs without the use of appnet services.

Hedera FileID is a triplet of numbers, e.g. `1.5.34634` represents file number `34634` within realm `5` within shard `1`.
Hedera TopicID follows a similar format and is defined as a triplet of numbers, e.g. `0.0.21243` represents a topic identifier `21243` within realm `0` within shard `0`.

##### Appnet Address Book
Each appnet that utilizes Hedera DID Method must define an address book file stored in Hedera File Service. The file contains URLs of appnet's servers that provide CRUD services for the DIDs within this network and Topic IDs within Hedera network used for posting DID and verifiable credentials. The address book is a JSON file compatible with a JSON schema defined [here](appnet-address-book.schema.json).

Example address book file content:
```json
{
	"appnetName": "My AppNet",
	"didTopicId": "0.0.1",
	"vcTopicId": "0.0.2",
	"appnetDIDServers": [
		"https://example.com/myappnet/did", 
		"https://example2.com/myappnet/did", 
		"https://example3.com/myappnet/did"
	]
}
```

## CRUD Operations
Hedera nodes support a Consensus Service API by which clients submit transaction messages to a topic. Valid and authorized messages on valid topics will be ordered by the consensus service, gossiped to the mirror net, and published (in order) to all subscribers (from the mirror net) on this topic. Every appnet that implements Hedera DID method must have a dedicated HCS topic created for DID documents registration. Appnets shall subscribe to their topics, capture and store valid DID document messages. A valid CRUD message of a DID document must have a JSON structure defined by a [did-message-schema](did-message.schema.json) and contains the following properties:
- `didOperation` - Operation to be performed on the DID document. Valid values are: `createOrUpdate` and `delete`.
- `mode` - Describes the mode in which DID document is provided in this message. Valid values are: `plain` or `encrypted`.
- `didDocumentBase64` - This field may contain either: 
  - a string that represents Base64-encoded plain DID document that conforms to the [DID Specification](https://w3c.github.io/did-core/),
  - or an encrypted representation of this Base64 string, where the encryption and decryption methods and keys are defined by appnet owners.
- `signature` - A signature that is a result of signing a string that represents Base64-encoded plain DID document with a private key corresponding to the public key `#did-root-key` in the DID document.

There is no on-chain mechanism that would validate incoming messages content, so it is a responsibility of the appnet's subscription logic to recognize and validate them based on the above criteria.


Here is an example message content:
```json
{
  "didOperation": "createOrUpdate",
  "mode": "plain",
  "didDocumentBase64": "ewogICJAY29udGV...9tL3ZjLyIKICAgIH0KICBdCn0=",
  "signature":  "QNB13Y7Q9...1tzjn4w=="
}
```

It is a responsibility of the appnet owners to decide who can submit messages to their DID topic. Access control of message submission is defined by a `submitKey` property of `ConsensusCreateTopicTransaction` body. Detailed information on Hedera Consensus Service APIs can be found in the official [Hedera API documentation](https://docs.hedera.com/hedera-api/consensus/consensusservice).


### Create
A DID document is created within the appnet by sending a `ConsensusSubmitMessage` transaction to Hedera node. It is executed by sending a `submitMessage` RPC call to HCS with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of appnet's DID topic
- `message` - a JSON DID message described above with `didOperation` set to `createOrUpdate`

DID Subjects, who have their own accounts on Hedera network and are authorized to submit messages to appnet's DID topic can send this transaction directly.
Other DID Subjects shall use appnet's defined relay interface.

```
TODO: Specify appnet's relay interface (REST API)?
```

### Read
A Hedera DID can be resolved by anyone on the network providing that DID documents were submitted to the appnet topic in a plain form.
In this case resolution requires reading the latest transaction message submitted to a DID topic for the given DID and checking if there was no earlier transaction for this DID with `didOperation` set to `delete`. This method can only be executed on a mirror node and may require reading a full history of messages in the topic. That is why it is only recommended in cases when resolving party does not trust an appnet or wants to verify its trust against the ledger.

In other cases it is much more performant to resolve the DID directly by calling appnet service. If appnet DID topic operated in an encrypted mode and the verifier is not in possession of a decryption key, appnet service is the only way of resolution.

```
TODO define appnet service and provide mirrornet API example for resolution
```

### Update
A DID document is updated within the appnet by sending a `ConsensusSubmitMessage` transaction to Hedera node. It is executed by sending a `submitMessage` RPC call to HCS with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of appnet's DID topic
- `message` - a JSON DID message described above with `didOperation` set to `createOrUpdate`

DID Subjects, who have their own accounts on Hedera network and are authorized to submit messages to appnet's DID topic can send this transaction directly.
Other DID Subjects shall use appnet's defined relay interface.

### Delete (Revoke)
A DID document is created within the appnet by sending a `ConsensusSubmitMessage` transaction to Hedera node. It is executed by sending a `submitMessage` RPC call to HCS with the `ConsensusSubmitMessageTransactionBody` containing:
- `topicID` - equal to the ID of appnet's DID topic
- `message` - a JSON DID message described above with `didOperation` set to `delete`

DID Subjects, who have their own accounts on Hedera network and are authorized to submit messages to appnet's DID topic can send this transaction directly.
Other DID Subjects shall use appnet's defined relay interface.

Appnets shall delete the DID document from their storage or mark it as revoked upon receiving this message from Hedera MirrorNet.

## Security Considerations
Security of Hedera DIDs inherits security properties of Hedera Hashgraph network itself and specific implementation of appnet creators.
Write access to Hedera Consensus Service DID Topics can be managed with advanced key structures and is defined by appnet administrators.

Hedera currently stores only the following data on the ledger:

* Public DIDs/DID Documents that include public keys and service endpoints
* Hashes of verifiable credentials issued by Hedera DID subjects.

All confidential information such as cryptographic private keys are stored with end consumers of Hedera DID.

## Privacy Considerations
For publicly available DIDs that can be resolved by anyone, care should be taken to ensure the DID Documents do not contain any sensitive personal information. One primary consideration is how do we support the deleting of personal information in Hedera network since it is effectively a blockchain. The answer is the same in this context as it is for other blockchain based DID systems: we do not store any personal information in the DID document. DID documents in Hedera should be limited to just public keys and service endpoints for key recovery.

## Reference Implementations
The code at [https://github.com/hashgraph/Identity-sdk](https://github.com/hashgraph/Identity-sdk) is intended to provide a Java SDK for this DID method specification. A set of unit tests within this repository presents a reference implementation of this DID method.

## References
* <https://w3c-ccg.github.io/did-spec/>
* <https://github.com/hashgraph/Identity-sdk> 
* <https://docs.hedera.com/hedera-api/>
* <https://www.hedera.com/>