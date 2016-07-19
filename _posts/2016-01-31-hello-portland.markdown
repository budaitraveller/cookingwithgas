---
layout: post
title:  "Tooling up for smart contract development with ethereum."
date:   2016-01-31 09:11:03
description: Tooling up for ethereum smart contract development with geth browser-solidity and thet ethereum testnet
thumbnail: person1.jpeg
categories: category1 category2

# Information for the author block
author: Nina Petropoulos
---

This article will walk you through developing contracts using:

The ethereum CLI tool "geth"
The solidity web IDE "browser-solidity"
The ethereum "testnet" blockchain.  

You will need to understand how to use the command line, how to write software, and the very basics of blockchain terminology and ethereum.

This is an article that will focus on "tooling up" - showing you the right tools to use.  There are a lot of tools out there in the ethereum development space - many of them are not worth investing in.  The tools we'll go over here are well worth learning.

###Step 1 

get geth

See: https://www.ethereum.org/cli

###Step 2

Launch geth as follows:

`geth --testnet --rpc --rpccorsdomain '*' console 2>> geth.log`

***Extra stuff:***

```
--testnet means "run on the testnet"
--rpc means "listen for rpc commands that come from outside"
--rpccorsdomain '*' ?I think means? allow things from not on your computer to talk to it... basically a big fat security hole. 
`console` means launch a console with this
```

The last bit "2>> geth.log" pushes the annoying blockchain/mining info to a log file, and lets you use the console.  ***You can also run geth in one terminal, and in the other, "geth attach"***  

If you get stuck: "exit"

If you think you will do this more than once, make a little script to set it up:

```
bash-3.2$ echo "geth --testnet --rpc --rpccorsdomain '*' console" > ~/bin/rungeth.sh
bash-3.2$ chmod +x ~/bin/rungeth.sh
```

###Step 3

Create and unlock an account: 

Do this outside of the geth console on the command line:

```
$ geth account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat Passphrase:
```

Now, to unlock your account, in the geth console (not on the command line) type this:

```
> eth.accounts
```

You will see the address of the account you just made.  You must unlock that account to use it.

Using the address of the account you just displayed, and the password you used to create it, type

```
personal.unlockAccount(0x9bcd2d4f8dddea8f4ff303fe2052fc134b655c8a, "your_password")
```

You will get the response ***undefined***.  Most of the time, undefined means success on the geth console.

Of course, save your password in a password database of your choosing.

***Extra stuff:***

See also: https://github.com/ethereum/go-ethereum/wiki/Managing-your-accounts

To make an alias for that account:

```
> var me = eth.accounts[0];
undefined
> me
"0x9bcd2d4f8dddea8f4ff303fe2052fc134b655c8a"
> 
```
Note that the response to a correct assignment is: ***undefined***

And further, that your accounts are now located here:

```
bash-3.2$ cd ~/Library/Ethereum/keystore/
bash-3.2$ ls -la
total 24
drwx------   5 rsb  staff  170 Jul 18 14:34 .
drwxr-xr-x  10 rsb  staff  340 Jul 18 14:43 ..
-rw-------   1 rsb  staff  491 Apr 16 23:25 UTC--2016-04-17T06-25-49.791304738Z--305fb0d6fcf12c715c6cab221a3fcbd32aa1c44f
-rw-------   1 rsb  staff  491 Apr 23 23:20 UTC--2016-04-24T06-20-40.267773067Z--882bd3a2e9d74110b24961c53777f22f1f46dc5d
-rw-------   1 rsb  staff  491 Jul 18 14:34 UTC--2016-07-18T21-34-51.694566352Z--da05d4c4fba23675e8d0a8d553075b0bb9dd0121
```

And note that the command "geth accounts list" does not do what you think it should (give you the account addresses, right?) - it gives you a unique index to the account that you won't use in this tutorial (or ever, most likely).  You can find your account addresses via the geth console:

```
> eth.accounts[0]
"0x9bcd2d4f8dddea8f4ff303fe2052fc134b655c8a"
```

Finally, a note on deleting accounts: Rather than blowing those files away to delete and account (there is no delete function) just encrypt them and move them aside:

```
    cd ~/Library/Ethereum/keystore/
    ls -la
    tar cvzf oldkeys.tgz  *
    openssl aes-256-cbc -a -salt -in oldkeys.tgz -out oldkeys.tgz.enc
    ls
    rm UTC*
    rm oldkeys.tgz
    mv oldkeys.tgz.enc ~/place_I_store_stuff
```

###Step 4

To continue, you are going to need to obtain the full testnet blockchain, and obtain some testnet ether.  We'll show you how to do that the old fashioned way, by syncing your blockchain and mining some ether.

Your copy of geth should already be downloading the full testnet from peers all over the world.  This will take a full day for most people, and require many gigabytes of data be downloaded.  Unless you have a friend you trust in physical proximity who can copy the blockchain to your computer, then you will have to wait.

While your copy of geth downloads the blockchain, you should see messages in the geth console talking about blocks, like so:

```
I0718 18:57:19.859391   43671 blockchain.go:1251] imported 1 block(s) (0 queued 0 ignored) including 0 txs in 4.389402ms. #1333027 [2f0e3825 / 2f0e3825]
```

The message above imported only one block.  When you are just starting to sync down the blockchain, you will see messages that more than one block has been imported (often 256 blocks).

When your testnet blockchain is fully synced, you will see:

* Messages reporting that one block at a time is being imported.
* The block number in each message (looks like: #1333027) will match the highest block mined on ethernet block explorer sites (like: https://testnet.etherscan.io/) 

####Mining the ether

In the geth console, type:

```
> miner.start()
```

Now check the geth log, after a few minutes, you should see some messages start to show up about "Generating DAG".  The "DAG" is a giant file that must be generated or obtained prior to mining.  When you get close to having the full file generated, you will see the percentage approach 100 in the geth logs:

```
I0718 19:27:02.221854   45253 ethash.go:252] Generating DAG: 96%
I0718 19:27:08.514340   45253 ethash.go:252] Generating DAG: 97%
I0718 19:27:14.770954   45253 ethash.go:252] Generating DAG: 98%
```

And you will see work committed on new blocks by your CPU, like so:

```
I0718 19:38:53.697866   45253 worker.go:569] commit new work on block 1333201 with 1 txs & 0 uncles. Took 44.675404ms
```

It could take hours, but usually within 20 minutes, you should see mining happening, like so:

```
I0718 19:46:48.537178   39568 worker.go:348] :hammer:  Mined block (#1333236 / 065dff46). Wait 5 blocks for confirmation
```

The ether will go into your primary account (after five blocks have confirmed it).  Your primary account is your first account, unless you set another account as your primary account.  Unlock your account if it's locked, and check the balance via the geth console:

```
eth.getBalance("0x9bcd2d4f8dddea8f4ff303fe2052fc134b655c8a")
```

####Problems you will run into

Check every hour or so to see if you are still downloading blocks.

Your copy of geth will not always be able to find peers.  If you see that it has stalled in downloading blocks, exit and restart.  You won't lose any blocks, you will just need to give it a kickstart in finding new peers again, and it will pick up where it left off.  If you network connection is very bad, this will happen more often.  If it is very good, this will happen less often.  

####Alternate method requiring help from a friend who is doing this with you:

You can speed this up if you or someone you know has an up-to-date copy of the testnet blockchain, and a account with testnet ether they can share with you.  That's the ideal scenario, because you won't have to wait for all this stuff.  

To use this method, copy your friends /chaindata directory contents into your /chaindata directory.

Then, run geth as before, and when have them send some ether to your account.  A few blocks later on the testnet, your ether will appear.

###Step 5

Go to the online compiler at : http://ethereum.github.io/browser-solidity/#version=soljson-latest.js

###Step 6

Create a contract... I suggest:

```
contract HelloWorld{
    string public message;
    
    function setMessage(string newMessage){
        message = newMessage;
    }
    
}
```

###Step 7

On the right hand side of the online compiler, click on the little tab that has a icon of a Box, which appears between the paper airplane and the cloud upload. (hereafter the block tab will be called the "Environment" tab)

###Step 8 

In the Environment tab select the radio button for **web3 provider**, and make sure web3 provider endpoint is set to `http://localhost:8545`

###Step 9

Click the red "create" button....

You should see: "Waiting for transaction to be mined..." just below it

If you see: 

  *"Invalid JSON RPC response:" 
     *That probably means geth isn't running, doesn't have the --rpc flag on it, is on a different port from 8545, or for some other reason can be found. **this error also occurs if you are using https to connect to the online editor** 

  *"Error: could not unlock signer account" 
     * it probably means you forgot to unlock your account... try step #3

###Step 10

Your contract is now technically deployed. You have two options, you can interact with it through the web editor, or through the geth console... Let's play with it on the geth console first....

###Step 11

In the web editor copy the text in the box "interface"... consider saving this text. It should look something like this: 

`[{"constant":false,"inputs":[{"name":"newMessage","type":"string"}],"name":"setMessage","outputs":[],"type":"function"},{"constant":true,"inputs":[],"name":"message","outputs":[{"name":"","type":"string"}],"type":"function"}]`

###Step 12

Go to the geth console, and create the contract

type:

`var helloWorld = eth.contract([{"constant":false,"inputs":[{"name":"newMessage","type":"string"}],"name":"setMessage","outputs":[],"type":"function"},{"constant":true,"inputs":[],"name":"message","outputs":[{"name":"","type":"string"}],"type":"function"}]
).at("<address>")`

`<address>` can be found either on an earlier line that should have appeared in your geth console stream when you clicked "create" in the online compiler. **note that it should be in double quotes inside the .at() call**

`> I0718 14:20:18.814556   38882 xeth.go:1026] Tx(0x67d0e5c297f1fc12272dede2484366e1ae7c70e254ea645a4bcd911cc7e46138) created: 0x34f560368e973679a4e8644ee7d25a961b8662bb` (0x34f560368e973679a4e8644ee7d25a961b8662bb is the address)

or in the online compiler 
under "HelloWorld at 0x34f560368e973679a4e8644ee7d25a961b8662bb (blockchain)"

###Step 13

Now you have the contract under helloworld. In the console type:
`> helloWorld` and you should see something like this:
```
{
  address: "0xafebc7b4b1d0d8bef87c20d9ed6b1739589a6a69",
  allEvents: function(),
  message: function(),
  setMessage: function()
}
```

###Step 14

Try getting the message from helloWorld, which is currently unset by calling:
`helloWorld.message.call()`

###Step 15

Try setting the message from helloWorld by calling:

`helloWorld.setMessage.sendTransaction("Greetings Blockchain", {from: eth.accounts[0]})`

This will cost you a little bit of gas on the testnet, maybe 200,000.  That would be real money (a couple US cents) on the mainnet. 

you **must wait for blocks to be mined before this takes effect**, I usually wait for at least **2** blocks to be mined because sometimes it doesn't get caught in the first block

###Step 16

Try `helloWorld.message.call()` again. 

###Step 17

Now lets try to use the online compiler to interact with it. This is useful, but I haven't figured out how to send money with transactions, which is a disadvantage for some of our more complicated contracts... but lets at least use the interface. because they are sort of fun

There should be a red button on the right hand side, that says "fallback" just below "HelloWorld at 0x34f560368e973679a4e8644ee7d25a961b8662bb (blockchain)". (You may have to toggle a little arrow at the right in order to see it) 

Below this button there should be a blue button that says "message" and a red button with a field that says "setMessage"

Click on the Blue Button that says "message" 
 you should see something like 
```
Value: "0x000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000144772656574696e677320426c6f636b636861696e000000000000000000000000"
Decoded:
string: Greetings Blockchain
```

###Step 18

Lets try setting the value now... type **"Hello from the online compiler"** into the field next to the red button called "setMessage". *be sure to include the quotes marks on the string*

click the setMessage button and then wait for the transaction to be mined

After the transaction is mined, try the blue "message" button again.

You should see your message in the interface.

###Other Stuff:

Testnet blockchain explorers (see your transactions on the testnet):

 https://testnet.etherscan.io/ 
 https://morden.ether.camp/ 
