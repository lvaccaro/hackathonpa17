# HackathonPA17
SMART CONTRACT - READY TO RUN IN < 24 HOURS

How to write Smart Contract? how deploy? which environment? private testnet? 
Make Smart Contract in \<24h. HackathonPA17, Rome 2017.

## Summary
A Smart Contract is a script code for Ethereum transaction in order to make an action (block / unblock money). 
In the Ethereum transactions there is a specific output field to contain the script.
The purpouse of this tutorial is to make a little introduction of the smart contracts environment for a fast and light execution and a daylife of a smart-contracts-developer.
I wnat explain also some tricks and errata corrige learned in Blockchain PA 2017 EY Hackathon, Rome 2017. 

## Documents
We discuss the following environments:
* Solidity: write your contracts in Solidity language.
* Truffle: light framework environment to run/deploy/test your contracts.
* Web3: nodejs library to connect your work into ethereum network, compile/deploy contracts and send transactions.
* TestRPC: an ethereum peer node for test.
* Ethereum Azure: how to deploy an ethereum full distribution on Azure platform.

## Project BANNER
Banner uses smart-contracts for competitive bid. We guarantee trasparency of the bids through a reputation mechanism.
The workflow is described by 5 main steps:
* publish the call for bids: setting the minimum money accepted, maximum number of partecipants and closing date.
* take bids: any company can subscribe it and insert his own bid. 
* voting system: the operators scrutinize the bids and they set, for each bid, 3 main scores by financial criteria, technical 	expertise and company reputation.
* winner: at the closing date, the smart-contract extracts automatically the winner: the bids with the best scores.
* reputation score: if the winner is honest and able to make the job (after a deep company and technical analisys by operators), increase his reputation score. If a winner is a liar (for example set a too low-cost bids), decrease the reputation score and the new winner is automatically the second classify.

Note: only at the end of the call for bids, all the bids are public available.

## Team
We achieve the 3 position on #HackathonPA17.
Thanks to all the members of the #Banner group:
* Thomas Rossi https://github.com/ThomasRossi
* Gianluca Leuzzi gianluca.Leuzzi@microsoft.com
* Agid , Consip, EY.

A personal greetings to all the microsoft support to stable our scripts and run in the Azure environment.  
https://twitter.com/SogeiUffStampa/status/847830084862038016  
![alt tag](https://pbs.twimg.com/media/C8QXZ_yXsAUuzLM.jpg:large)
