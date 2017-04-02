# WEB3.JS


To install ethereum default library for nodejs:
```
npm install web3
```

I formalize a flexible and ready to work procedure.

REMEMBER: use function to compile solidity contract on the ethereum server node, could be an error, because your ethereum node could not have solc (solidity) installed


#### Requirements
```
var Web3 = require('./node_modules/web3/lib/web3');
var fs = require('fs');
var sleep = require('sleep');
var solc = require('solc');
```

#### Import ethereum

```
if (typeof window !== 'undefined' && typeof window.Web3 === 'undefined') {
   window.Web3 = Web3;
}
module.exports = Web3;
var web3 = new Web3();
web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545'));
web3.eth.defaultAccount=web3.eth.accounts[0]
console.log("default account: "+web3.eth.defaultAccount);
console.log("latest block: ");
console.log(web3.eth.getBlock("latest"));
```
*web3* allow you to connect directly to Ethereum node. You can select the node throught the HttpProvider() function.
The coinbase account is the first address generated in the systsem with the initial coins; I call it the defaultAccount.
I print also the information related the last block mined in the node. Each time you reset the etherum node, these values change.

#### Accounts availables
```
console.log("ACCOUNTS");
var balance = (acct) => { return web3.fromWei(web3.eth.getBalance(acct), 'ether').toNumber()}
var accounts = web3.eth.accounts;
accounts.forEach(function(account) {
    console.log(account+" -> "+balance(account));
});
```
Print a list of account generated in the system with the internal balance. The first account showed is the defaultAccount. When you start an etherum node from scretch, only the defaultAccount as balance greeter than 0.

#### Parsing contract
```
console.log("PARSING CONTRACT");
var source = "";
try {
    source = fs.readFileSync('./contracts/Simple.sol', 'utf8');
} catch(e) {
    console.log('Error:', e.stack);
}
```
There are several ways to import your Solidity contract. My favourite solution is to read the solidity file; in alternative you can copy your contract inside the source variable.

#### Compile contract
``` 
var compiled,abi,code,contractObject;
try {
  compiled = solc.compile(source,1);
  abi = JSON.parse(compiled.contracts[":SimpleStorage"].interface);
  code = '0x'+compiled.contracts[":SimpleStorage"].bytecode;
} catch(e) {
    console.log('Error:', e.stack);
    return;
}
```
* The compilation procedure returns an array of contracts defined in you source. If your source defined only 1 contract, you can ommit the *contracts[":SimpleStorage"]* and use directly the internal property of the contract (compiled.interface or compiled.bytecode)
* ABI is the Application binary interface for your smart-contract. This object is returned as JSON string, so you need to parsify.
* CODE is the bytecode of your contract to execute. By default the library cut the '0x', so you should manually add it.
* With *solc* library you can compile the contract on client-side. This is important because you can also decide to compile the contract on server-side as the follow:
```
var compiled = web3.eth.compile.solidity(source);
```
In this case, the ethereum server node makes all the job, so you should have installed solc software on the server. In Azure environment, solc is not installed by default, so you retrieve the follow error: "command not found." . Once you have installed solc package remember to add the binary to the default path. Note solc binary has name solcjs, so you should change the current binary name to solc.   

#### Unlock ethereum account
```
console.log("UNLOCK ACCOUNT");
web3.personal.unlockAccount (web3.eth.defaultAccount, "********" , 15000, (error, result) => {
  if(!error){
     ....
  } else {
     console.log("Error: "+error);
  }
});
```
By default, your accounts in Ethereum are "locked," which means that you can't send transactions from them. You need to unlock an account in order to send transactions from it through Geth directly or via RPC. In order to unlock an account, you'll need to provide the password, which is used to decrypt the private key associated with your account, hence allowing you to sign transactions.

#### Run you contract
```
try{
      var contractObject = web3.eth.contract(abi);
      var stuff={from: web3.eth.defaultAccount, data: code , gas:1000000};
      contractInstance = contractObject.new(stuff,
          function(e, contractInstance){

          if(!e) {
            if(!contractInstance.address) {
              console.log("Contract transaction send: TransactionHash: " + contractInstance.transactionHash + " waiting to be mined...");
            } else {
              console.log("Contract mined! Address: " + contractInstance.address);
              ...
            }
          } else{
            console.log("Error:"+e);
            console.log(contractInstance);
          }
      });

    } catch(e) {
        console.log('Error:', e.stack);
        return;
    }
```
* contractObject : is the class of your smart-contract.
* contractInstance : is the object generated from contractObject. To instantiate a smart-contract, you should make a ethereum transaction specified the standard parameters to build your contract: the sender address (in this case the coinbase address), the data (the bytecode to put inside the script field ) and the amount.
* the amount > the minimum fee of the system and the amount > the fee calculated for your smart-contract. Bigger is you smart-contract, bigger are the fees you have to spend. In my test the minimum amount of gase accepted are 21000. The gas value in the example is the standard used gas value of the Azure Etherum dev.
* the callback is called twice : 1st when the transaction is broadcasted, 2nd when the block, containing the transaction, is mined. Only at the 2nd callback, you can call successfully the observer function in the contract in order to read his intenal state.

#### Read internal state
```
function read(contractInstance){
  console.log("READ");
  try{
    contractInstance.read(function(e,data){
      if(!e){
         console.log('Data: ', data);
      } else {
         console.log('Error: ', e);
      }
    });
  }catch(e) {
    console.log('Error: ', e.stack);
    return;
  }
}
```
The contractInstance object created before is just put inside a transaction and mined. So I can call observer and modifier functions. The read() is an observer and I can use it free without pay any fee.
