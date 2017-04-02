# Solidity

Each smart contract could be divided in 3 main areas:
* memory: contains the variable/property of the specific contract.
* modifiers: functions allowed to write values inside memory, edit variables and change the current status of the blockchain. You need a send a transaction, pay gas, in order to set values.
* observers: caller functions can only read the variables and never writing. A caller function can’t change the current status of the blockchain, so they has no cost. In other word, you can call observer how many time you want without pay, because you don’t need to make a transaction to read the current status of the blockchain.

REMEMBER:
* a contract is stored inside the script field inside the transaction.
* you have limited space and there isn’t a stripe to compress your script
* start your contract file with “pragma solidity ^0.4.8;” in order to select the version of solidity to use. Solidity version < 0.4.0 is really different with lots hole of retro-compatibility. 
* To check your solidity version “solcjs —version” ; the path depend where you have install.


BEST PRACTISES:
* split your big contract in smaller contracts
* take care of your resources, never made memory/blocks(bitcoin) unlimited.


### K.I.S.S. Difficulty:
Storage example to write and read a variable inside smart contract. You pay gas to write variable and send the transaction; in other hand, you have unlimited reading operations.
```
contract SimpleStorage {
    uint storedData;

    function set (uint x)  PUBLIC PAYABLE {
        storedData = x;
    }

    function get() constant returns (uint retVal)  {
        return storedData;
    }
}
```

NOTE:
* the SET() function is an modifier: mark this operation as payable and public callable ( it is not an must, but it is better you do)
* the GET() function is an observer: mark this operations as constant, so you can call GET() directly from external without pass through .call() function.

### HelloWorld Difficulty: 
It is the most common smart contract for money transfer. It is the standard solidity-boiler-template and I don’t put any tricks or changes. This Smart Contract maps a money transfert operation from the sender user, identified by the sender address of the ethereum transaction, and the receiver user, passed through the sendCoin() function on the smart contract.
Remember the new balance will be available only when then block, which contains the transaction, will be mined.
```
contract MetaCoin {
	mapping (address => uint) balances;

	event Transfer(address indexed _from, address indexed _to, uint256 _value);

	function MetaCoin() {
		balances[tx.origin] = 10000;
	}

	function sendCoin(address receiver, uint amount) returns(bool sufficient) {
		if (balances[msg.sender] < amount) return false;
		balances[msg.sender] -= amount;
		balances[receiver] += amount;
		Transfer(msg.sender, receiver, amount);
		return true;
	}

	function getBalanceInEth(address addr) returns(uint){
		return ConvertLib.convert(getBalance(addr),2);
	}

	function getBalance(address addr) returns(uint) {
		return balances[addr];
	}
}
```

NOTE:
* Memory structure is an Hashmap of address and number, used to store the balance of coins.
* Transfert(): external event to make interrupt notification
* Constructor metacoin(): define the default value of the internal variables;
* In sendcoin()/metacoin() you can retrieve the message information of the current transfaction (msg.sender, tx.origin)

### Brainfuck Difficulty: 
Manage multiple Smart Contracts in All-in-One solidity file. This is a piece of solidity file develops in the hackathon.
 
```
contract Requests {
    struct RequestStruct {
      	bytes32 companyName;
      	uint fund;
      	uint createdAt;
      	bool dead;
      	uint score;
    }
    mapping (address => RequestStruct) public requests;
    address[] requestsAddress; 		 // list of address used for the requests    	   
    uint requestsCount;         		// number of the requests

    function subscribe(address addressRequest, bytes32 _companyName,  uint _fund) returns(bool) {
        RequestStruct memory newRequest;
        newRequest.companyName = _companyName;
        newRequest.fund=_fund;
        newRequest.createdAt=now;
        newRequest.dead=false;
        newRequest.score=0;
        requests[addressRequest] = newRequest;
        requestsAddress.push(addressRequest);
        requestsCount++;
        return true;
    }

    function get(address addressRequest) returns(bytes32, uint, uint) {
      if ( requests[addressRequest] == null){
        throw;
      }
      return (requests[addressRequest].companyName, requests[addressRequest].fund, requests[addressRequest].createdAt );
    }

    function getAll(bool block) public constant returns (bytes32[], uint[], uint[], uint[], uint[], uint[]) {
      uint length = requestsCount;
      if(block==true){
        length=0;
      }

      bytes32[] memory companyNames = new bytes32[](length);
      uint[] memory createdAts = new uint[](length);
      uint[] memory funds = new uint[](length);
      uint[] memory score = new uint[](length);
      for (uint i=0 ;i < length; i++){
        RequestStruct memory currentRequest;
        currentRequest = requests[ requestsAddress[i] ];

        if(currentRequest.subscribed==true){
          companyNames[i]=currentRequest.companyName;
          createdAts[i]=currentRequest.createdAt;
          funds[i]=currentRequest.fund;
          score[i]=currentRequest.score;
        }
      }
      return (companyNames, createdAts, funds, score);
    }
}
```

```
contract Announcement {

    struct AnnouncementStruct {
            uint code;            // unique number

            bytes32 title;        // title
            bytes32 description;  // description

            uint createdAt;       // cration date
            bool completed;       // is completed
            uint closedSubscriptionAt;  // closing subscription date

            uint minFunds;        // min amount of money accepted
            uint maxRequests;     // max number of request accepted:

            Requests requests;
            address[] requestsAddress;
            uint requestsCount;
    }

    AnnouncementStruct public announcement;

    // Owner of the announcement :
    address public chairperson;

}
        function read(uint code) constant returns (uint, bytes32 , bytes32 , uint , bool , uint, uint, uint, bool, uint ){
              return (
                announcement.code
                announcement.title,
                announcement.description,
                announcement.createdAt,
                announcement.completed,
                announcement.closedSubscriptionAt,
                announcement.minFunds,
                announcement.maxRequests,
                announcement.locked
                announcement.requestsCount);

        }
……
```

NOTE:
* STRUCT is the subset of your variables who identify a group.
* STRUCT internal of a smart contract cannot be used in another one.
* MAPPING are hash map not iterable. If you want iterate you need array structure.
* RETURN it is a best practices return array of basic primitive objects
* CHAIRPERSON is the owner who crate the instance of smart contract

