• [Setting up a Project](#index1)  
• [First contract](#index2)  
• [Compiling Solidity](#index3)  
• [Using OpenZeppelin Contracts](#index4)  
• [Importing OpenZeppelin Contracts](#index5)  
• [truffle Tutorials 教程](#index98)    
• [Contact 联系方式](#index99)   

# <span id='index1'>• Setting up a Project</span>  
The first step after creating a project is to install a development tool.

The most popular development framework for Ethereum is Hardhat, and we cover its most common use with ethers.js. The next most popular is Truffle which uses web3.js. Each has their strengths and it is useful to be comfortable using all of them.

In these guides we will show how to develop, test and deploy smart contracts using Truffle and Hardhat.

To get started with Truffle we will install it in our project directory.
```
$ npm install --save-dev truffle
```

Once installed, we can initialize Truffle. This will create an empty Truffle project in our project directory.
```
npx truffle init

Starting init...
================

> Copying project files to /home/openzeppelin/learn

Init successful, sweet!
```

# <span id='index2'>• First contract</span>  
We store our Solidity source files (.sol) in a contracts directory. This is equivalent to the src directory you may be familiar with from other languages.

We can now write our first simple smart contract, called Box: it will let people store a value that can be later retrieved.

We will save this file as contracts/Box.sol. Each .sol file should have the code for a single contract, and be named after it.
```
// contracts/Box.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Box {
    uint256 private _value;

    // Emitted when the stored value changes
    event ValueChanged(uint256 value);

    // Stores a new value in the contract
    function store(uint256 value) public {
        _value = value;
        emit ValueChanged(value);
    }

    // Reads the last stored value
    function retrieve() public view returns (uint256) {
        return _value;
    }
}
```

# <span id='index3'>• Compiling Solidity</span>  
The Ethereum Virtual Machine (EVM) cannot execute Solidity code directly: we first need to compile it into EVM bytecode.

Our Box.sol contract uses Solidity 0.8 so we need to first configure Truffle to use an appropriate solc version.

We specify a Solidity 0.8 solc version in our truffle-config.js.
```
// truffle-config.js
  ...

  // Configure your compilers
  compilers: {
    solc: {
      version: "0.8.4",    // Fetch exact version from solc-bin (default: truffle's version)
      // docker: true,        // Use "0.5.1" you've installed locally with docker (default: false)
      // settings: {          // See the solidity docs for advice about optimization and evmVersion
      //  optimizer: {
      //    enabled: false,
      //    runs: 200
      //  },
      //  evmVersion: "byzantium"
      // }
    }
  },

  ...
```

Compiling can then be achieved by running a single compile command:
```
npx truffle compile

Compiling your contracts...
===========================
✔ Fetching solc version list from solc-bin. Attempt #1
✔ Downloading compiler. Attempt #1.
> Compiling ./contracts/Box.sol
> Compiling ./contracts/Migrations.sol
> Artifacts written to /home/openzeppelin/learn/build/contracts
> Compiled successfully using:
   - solc: 0.8.4+commit.c7e474f2.Emscripten.clang
```

The compile command will automatically look for all contracts in the contracts directory, and compile them using the Solidity compiler using the configuration in truffle-config.js.

You will notice a build/contracts directory was created: it holds the compiled artifacts (bytecode and metadata), which are .json files. It’s a good idea to add this directory to your .gitignore.

# <span id='index4'>• Using OpenZeppelin Contracts</span>  
Reusable modules and libraries are the cornerstone of great software. OpenZeppelin Contracts contains lots of useful building blocks for smart contracts to build on. And you can rest easy when building on them: they’ve been the subject of multiple audits, with their security and correctness battle-tested.

Many of the contracts in the library are not standalone, that is, you’re not expected to deploy them as-is. Instead, you will use them as a starting point to build your own contracts by adding features to them. Solidity provides multiple inheritance as a mechanism to achieve this: take a look at the Solidity documentation for more details.

For example, the Ownable contract marks the deployer account as the contract’s owner, and provides a modifier called onlyOwner. When applied to a function, onlyOwner will cause all function calls that do not originate from the owner account to revert. Functions to transfer and renounce ownership are also available.

When used this way, inheritance becomes a powerful mechanism that allows for modularization, without forcing you to deploy and manage multiple contracts.

# <span id='index5'>• Importing OpenZeppelin Contracts</span>  
The latest published release of the OpenZeppelin Contracts library can be downloaded by running:
```
$ npm install @openzeppelin/contracts
```

You should always use the library from these published releases: copy-pasting library source code into your project is a dangerous practice that makes it very easy to introduce security vulnerabilities in your contracts.

To use one of the OpenZeppelin Contracts, import it by prefixing its path with @openzeppelin/contracts. For example, in order to replace our own Auth contract, we will import @openzeppelin/contracts/access/Ownable.sol to add access control to Box:
```
// contracts/Box.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Import Ownable from the OpenZeppelin Contracts library
import "@openzeppelin/contracts/access/Ownable.sol";

// Make Box inherit from the Ownable contract
contract Box is Ownable {
    uint256 private _value;

    event ValueChanged(uint256 value);

    // The onlyOwner modifier restricts who can call the store function
    function store(uint256 value) public onlyOwner {
        _value = value;
        emit ValueChanged(value);
    }

    function retrieve() public view returns (uint256) {
        return _value;
    }
}
```

The OpenZeppelin Contracts documentation is a great place to learn about developing secure smart contract systems. It features both guides and a detailed API reference: see for example the Access Control guide to know more about the Ownable contract used in the code sample above.

# <span id='index98'>• truffle Tutorials 教程</span>  
CN 中文 Github  [truffle 教程 : github.com/565ee/truffle_CN](https://github.com/565ee/truffle_CN)  
CN 中文 CSDN    [truffle 教程 : blog.csdn.net/wx468116118](https://blog.csdn.net/wx468116118/category_12007659.html)  
EN 英文 Github  [truffle Tutorials : github.com/565ee/truffle_EN](https://github.com/565ee/truffle_EN)  

# <span id='index99'>• Contact 联系方式</span>  
Homepage   : [565.ee](https://565.ee)  
GitHub     : [github.com/565ee](https://github.com/565ee)  
Email      : 565.eee@gmail.com  
Facebook   : [facebook.com/565.ee](https://facebook.com/565.ee)  
Twitter    : [twitter.com/565_eee](https://twitter.com/565_eee)  
Telegram   : [t.me/ee_565](https://t.me/ee_565)
