• [Setting up a Local Blockchain](#index1)  
• [Deploying a Smart Contract](#index2)  
• [Sending transactions](#index3)  
• [Querying state](#index4)  
• [Setup](#index5)  
• [Getting a contract instance](#index6)  
• [Calling the contract](#index7)  
• [Sending a transaction](#index8)  
• [truffle Tutorials 教程](#index98)    
• [Contact 联系方式](#index99)  

# <span id='index1'>• Setting up a Local Blockchain</span>  
Before we begin, we first need an environment where we can deploy our contracts. The Ethereum blockchain (often called "mainnet", for "main network") requires spending real money to use it, in the form of Ether (its native currency). This makes it a poor choice when trying out new ideas or tools.

To solve this, a number of "testnets" (for "test networks") exist: these include the Ropsten, Rinkeby, Kovan and Goerli blockchains. They work very similarly to mainnet, with one difference: you can get Ether for these networks for free, so that using them doesn’t cost you a single cent. However, you will still need to deal with private key management, blocktimes in the range of 5 to 20 seconds, and actually getting this free Ether.

During development, it is a better idea to instead use a local blockchain. It runs on your machine, requires no Internet access, provides you with all the Ether that you need, and mines blocks instantly. These reasons also make local blockchains a great fit for automated tests.

The most popular local blockchain is Ganache. To install it on your project, run:
```
$ npm install --save-dev ganache-cli
```

Upon startup, Ganache will create a random set of unlocked accounts and give them Ether. In order to get the same addresses that will be used in this guide, you can start Ganache in deterministic mode:
```
$ npx ganache-cli --deterministic
```

Ganache will print out a list of available accounts and their private keys, along with some blockchain configuration values. Most importantly, it will display its address, which we’ll use to connect to it. By default, this will be 127.0.0.1:8545.

Keep in mind that every time you run Ganache, it will create a brand new local blockchain - the state of previous runs is not preserved. This is fine for short-lived experiments, but it means that you will need to have a window open running Ganache for the duration of these guides. Alternatively, you can run Ganache with the --db option, providing a directory to store its data in between runs.

# <span id='index2'>• Deploying a Smart Contract</span>  
In the Developing Smart Contracts guide we set up our development environment.

If you don’t already have this setup, please create and setup the project and then create and compile our Box smart contract.

With our project setup complete we’re now ready to deploy a contract. We’ll be deploying Box, from the Developing Smart Contracts guide. Make sure you have a copy of Box in contracts/Box.sol.

Truffle uses migrations to deploy contracts. Migrations consist of JavaScript files and a special Migrations contract to track migrations on-chain.

We will create a JavaScript migration to deploy our Box contract. We will save this file as migrations/2_deploy.js.
```
// migrations/2_deploy.js
const Box = artifacts.require('Box');

module.exports = async function (deployer) {
  await deployer.deploy(Box);
};
```

Before we deploy we need to configure the connection to ganache. We need to add a development network for localhost and port 8545 which is what our local blockchain is using.
```
// truffle-config.js
module.exports = {
...
  networks: {
...
    development: {
     host: "127.0.0.1",     // Localhost (default: none)
     port: 8545,            // Standard Ethereum port (default: none)
     network_id: "*",       // Any network (default: none)
    },
    ...
```

Using the migrate command, we can deploy the Box contract to the development network (Ganache):
```
// truffle-config.js
module.exports = {
...
  networks: {
...
    development: {
     host: "127.0.0.1",     // Localhost (default: none)
     port: 8545,            // Standard Ethereum port (default: none)
     network_id: "*",       // Any network (default: none)
    },
    ...
```

使用该migrate命令，我们可以将Box合约部署到development网络（Ganache）：
```
npx truffle migrate --network development

Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.



Starting migrations...
======================
> Network name:    'development'
> Network id:      1619762548805
> Block gas limit: 6721975 (0x6691b7)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
...

2_deploy.js
===========

   Deploying 'Box'
   ---------------
   > transaction hash:    0x25b0a326bfc9aa64be13efb5a4fb3f784ffa845c36d049547eeb0f78e0a3108d
   > Blocks: 0            Seconds: 0
   > contract address:    0xCfEB869F69431e42cdB54A4F4f105C19C080A601
...
```

Truffle will keep track of your deployed contracts, but it also displays their addresses when deploying (in our example, 0xCfEB869F69431e42cdB54A4F4f105C19C080A601). These values will be useful when interacting with them programmatically.
All done! On a real network this process would’ve taken a couple of seconds, but it is near instant on local blockchains.

Remember that local blockchains do not persist their state throughout multiple runs! If you close your local blockchain process, you’ll have to re-deploy your contracts.

# <span id='index3'>• Sending transactions</span>  
Box's first function, store, receives an integer value and stores it in the contract storage. Because this function modifies the blockchain state, we need to send a transaction to the contract to execute it.

We will send a transaction to call the store function with a numeric value:
```
truffle(development)> await box.store(42)
{ tx:
   '0x5d4cc78f5d5eac3650214740728192ac760978e261962736289b10da0ec0ea43',
...
       event: 'ValueChanged',
       args: [Result] } ] }
```

Notice how the transaction receipt also shows that Box emitted a ValueChanged event.

# <span id='index4'>• Querying state</span>  
Box's other function is called retrieve, and it returns the integer value stored in the contract. This is a query of blockchain state, so we don’t need to send a transaction:
```
truffle(development)> await box.retrieve()
<BN: 2a>
```
Because queries only read state and don’t send a transaction, there is no transaction hash to report. This also means that using queries doesn’t cost any Ether, and can be used for free on any network.

Our Box contract returns uint256 which is too large a number for JavaScript so instead we get returned a big number object. We can display the big number as a string using (await box.retrieve()).toString().
```
truffle(development)> (await box.retrieve()).toString()
'42'
```

# <span id='index5'>• Setup</span>  
Let’s start coding in a new scripts/index.js file, where we’ll be writing our JavaScript code, beginning with some boilerplate, including for writing async code.
```
// scripts/index.js
module.exports = async function main (callback) {
  try {
    // Our code will go here

    callback(0);
  } catch (error) {
    console.error(error);
    callback(1);
  }
};
```

We can test our setup by asking the local node something, such as the list of enabled accounts:
```
// Retrieve accounts from the local node
const accounts = await web3.eth.getAccounts();
console.log(accounts)
```

We won’t be repeating the boilerplate code on every snippet, but make sure to always code inside the main function we defined above!
Run the code above using truffle exec, and check that you are getting a list of available accounts in response.
```
npx truffle exec --network development ./scripts/index.js
Using network 'development'.

[ '0x90F8bf6A479f320ead074411a4B0e7944Ea8c9C1',
  '0xFFcf8FDEE72ac11b5c542428B35EEF5769C409f0',
...
]
```

These accounts should match the ones displayed when you started the local blockchain earlier. Now that we have our first code snippet for getting data out of a blockchain, let’s start working with our contract. Remember we are adding our code inside the main function we defined above.

# <span id='index6'>• Getting a contract instance</span>  
In order to interact with the Box contract we deployed, we’ll use the Truffle contract abstraction, a JavaScript object that represents our contract on the blockchain.
```
// Set up a Truffle contract, representing our deployed Box instance
const Box = artifacts.require('Box');
const box = await Box.deployed();
```
We can now use this JavaScript object to interact with our contract.

# <span id='index7'>• Calling the contract</span>  
Let’s start by displaying the current value of the Box contract.

We’ll need to call the retrieve() public method of the contract, and await the response:
```
// Call the retrieve() function of the deployed Box contract
const value = await box.retrieve();
console.log('Box value is', value.toString());
```
This snippet is equivalent to the query we ran earlier from the console. Now, make sure everything is running smoothly by running the script again and checking the printed value:
```
$ npx truffle exec --network development ./scripts/index.js
Using network 'development'.

Box value is 42
```

# <span id='index8'>• Sending a transaction</span>  
We’ll now send a transaction to store a new value in our Box.

Let’s store a value of 23 in our Box, and then use the code we had written before to display the updated value:
```
// Send a transaction to store() a new value in the Box
await box.store(23);

// Call the retrieve() function of the deployed Box contract
const value = await box.retrieve();
console.log('Box value is', value.toString());
```

In a real-world application, you may want to estimate the gas of your transactions, and check a gas price oracle to know the optimal values to use on every transaction.
We can now run the snippet, and check that the box’s value is updated!

```
$ npx truffle exec --network development ./scripts/index.js
Using network 'development'.

Box value is 23
```

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
