# ETHEREUM NODE AZURE


* Deploy a standard ethereum distribution
* Open the port 8584

NOTE:
the mining time is different from testrpc to ethereum private/ public network. In TESTRPC you wait few seconds, with an real ethereum infrastructure you have to wait max 2 minutes from the broadcast of the transaction and the mining of the block containing.
remember to unlock your ethereum account with the ethereum password: web3.personal.unlockAccount(web3.eth.defaultAccount, “********” , 15000,(error,result)=>{ … });
