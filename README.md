# Hedera™ Hashgraph DID Method

[![License: Apache-2.0](https://img.shields.io/badge/license-Apache--2.0-green)](LICENSE)

This repository contains the Hedera Hashgraph DID method specification.

## DID Method Specification

The current version of the Hedera Hashgraph DID Method Specification can be found [here](hedera-did-method-specification.md).

Older versions can be viewed from tagged versions of this repo; 
* [v0.1](https://github.com/Meeco/hedera-did-method/releases/tag/v0.1) 
* [v0.9](https://github.com/Meeco/hedera-did-method/releases/tag/v0.9)

Note the specification markdown file uses [pandoc `title block` syntax](https://pandoc.org/MANUAL.html#extension-pandoc_title_block) for the title and meta data so the table of content did not include these headers.

To create a PDF of the document run
```
pandoc hedera-did-method-specification.md -o hedera-did-method-specification.pdf
```

Dependencies are:
``` 
sudo apt install pandoc librsvg2-bin texlive-latex-extra
```

## SDKs supporting the Hedera Hashgraph DID Method & Verifiable Credentials

- JavaScript SDK is maintained in a separate repository at [did-sdk-js](https://github.com/hashgraph/did-sdk-js).
- Java SDK is maintained in a separate repository at [did-sdk-java](https://github.com/hashgraph/did-sdk-java).


## License Information

Licensed under Apache License, Version 2.0 – see [LICENSE](LICENSE) in this repo or on the official Apache page  <http://www.apache.org/licenses/LICENSE-2.0>
