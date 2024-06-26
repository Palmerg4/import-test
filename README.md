## Ambiguous import minimal example

## Description

Some circular imports are not caught by the compiler until importing them as a submodule. I've included a pubic repo that can be used to reproduce. When importing from local files, the Ambiguous import error is not present. When importing the same files from a submodule, the Ambiguous import error is emitted. This is inconsistent and makes for an unfortunate masking of circular imports. I initially thought this was a foundry issue, but this happens when compiling with foundry build and with CLI solc. 

## Environment

- Compiler version: 0.8.25
- Target EVM version (as per compiler settings): Cancun
- Framework/IDE (e.g. Truffle or Remix): Foundry
- EVM execution environment / backend / blockchain client: During compilation 
- Operating system: MacOS Sonoma 14.4.1 (23E224)

## Steps to Reproduce

Grab this repo with minimal code reproduction, very simple circular imports

```
$ git clone https://github.com/Palmerg4/import-test
```

```
$ forge install
```
Either forge build or solc src/ContractA.sol, both result in the same successful compilation.
```
$ forge build
```
```
$ solc src/ContractA.sol
```

Remove comments on imports in src/ SubmoduleContractA.sol
```solidity
import "lib/import-test/src/ContractB.sol";
import "lib/import-test/src/ContractC.sol";
```

Either forge build or solc src/ContractA.sol, both result in the same unsuccessful compilation.
```
$ forge build
```
```
$ solc src/ContractA.sol
```
This error can also be produced by changing the import statement in src/ContractA.sol as follows:
```solidity
import "Src/ContractC.sol";
```
The uppercase 'S' in 'Src' will give the correct Identifier already declared error which should be present from the original import statement.

If you don't want to use this repo, here are the contracts inside:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.24;

import "src/ContractB.sol";
import "src/ContractA.sol";

contract ContractC {}
```

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.24;

import "src/ContractA.sol";
import "src/ContractC.sol";

contract ContractB is ContractC {}
```

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.24;

import "src/ContractB.sol";
import "src/ContractC.sol";

contract ContractA is ContractB {}
```

Now you'll need a submodule of these. Either install the previously mentioned repo or create your own and forge install the repo.

```
$ forge install https://github.com/Palmerg4/import-test
```
Or

```
forge install https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO
```
