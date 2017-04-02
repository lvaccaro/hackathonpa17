# Truffle

* Install truffle framework with : 
```
sudo npm install -g truffle .
```

* Repo creation: 
```
mkdir newrepo
cd newrepo
truffle init
```
you can add the ./app folder with a boiler-template fronted with the command truffle init webpack. 
( most guide uses an older version of Truffle with the ./app folder is always available in the standard init command)
* Write your contract:
```
truffle create contract SmartContractName
```
a new .sol file is created in ./contracts folder
* add your contract to the migration script: ./migrations/2_deply_contracts.js
```
var SmartContractName = artifacts.require(“./SmartContractFile.sol");
deployer.deploy(SmartContractName);
```
* Get available your smart contract:  
```
truffle compile && truffle migrate —reset 
```
REMEMBER: the reset option is needed each time you restart your ethereum node
* Test your contract in the easy and fast Truffle environment : truffle console
(REMEMBER: each time you make a migration you need to restart your truffle console)
* Run your system on localhost using a lite nodes web server for the fronted with: truffle serve 
(REMEMBER: when you change your file, it automatically reload the pages)
* Test: use ./test folder to write your test in mocha-like style :
```
it("should put 10000 MetaCoin in the first account", function() {
    return MetaCoin.deployed().then(function(instance) {
      return instance.getBalance.call(accounts[0]);
    }).then(function(balance) {
      assert.equal(balance.valueOf(), 10000, "10000 wasn't in the first account");
    });
  });
```

### Truffle Terminal:
the history of a previous session doesn’t work
some guide use an older Truffle version with synchronized function. They don’t work anymore.
each action is an asynchronized function: you could use the Promise cascade workflow. Remember to retrieve the real value of a datatype with valueOf().
 MetaCoin.deployed().then( instance => return instance.getBalance.call(accounts[0])).then(function(balance) { console.log(balance.valueOf()); });
- extract the ABI content (check it start with 0x to use with web3)
truffle(development)> Ballot.deployed().then( ballot => console.log(JSON.stringify(ballot.abi)))
- extract the coin base address (you should add the ‘0x’ to use with web3) 
truffle(development)> Ballot.deployed().then( ballot => console.log(JSON.stringify(ballot.address)))


### Truffle Etherum connection:
connection to your ethereum node: run for testing testrpc and set the truffle configuration file ./truffle.js like the follow: 
```
networks: {
    development: {
      host: 'localhost',
      port: 8545,
      network_id: '*' // Match any network id
    }
  }
```
