# Getting a real interest rate
In the last Quest on [Questbook.app](https://questbook.app) we created a smart account, where anyone could add a balance and start earning interest. If you’ve not done that yet, you should go complete that Quest first, because that is what we’re going to use in this Quest.

There was a major flaw in the previous contract we wrote. We did provide an interest for all deposits made on our account, but the interest was coming from the pocket of the creator of the contract (you). If you remember, you had to add some Ethers additionally to make withdrawals with interest possible.

But, that is not how real banks work. Banks are able to give interest on deposits based on the interests they earn by lending loans. How do we do that?

Fortunately there is a project called Compound. Compound is a multi Billion dollar project that is nothing but a few smart contracts written that facilitates giving loans and earning interest. So instead of re-inventing the wheel, we will use Compound and earn real interest using their contracts. That’s the beauty of web3. You can build on top of other’s contracts without their permission!

Just to give a background on Compound, Compound has defined a set of rules using smart contracts for people to park money and for other people to take loans and pay interest. That way people who have parked money earn interest. So we’ll collect ethers from our users and park it on compound. Then when they want to withdraw, we’ll take the interest from Compound and give it to the user.
## Finding the address of compound contract
Let’s go to compound’s official documentation

[https://compound.finance/docs](https://compound.finance/docs)

If you scroll to the bottom it shows you the address of all the contracts that have been deployed by Compound. There is a section called networks. 

You’ll see multiple networks like “Mainnet”, “Rinkeby”, … “Ropsten”.

Mainnet is the actual Ethereum. There are other test nets of Ethereum where developers can play around before deploying to actual Ethereum (mainnet). 

Under mainnet you will see many rows. Look for cETH.

Compound allows you to deposit various crypto currencies and earn interest on them. We will be using only Ethers as the currency people can deposit money to our smart bank account.

Copy the address against cETH. (stands for compound-ETH). Copy this address and visit : 

[https://etherscan.io/address/0x4ddc2d193948926d02f9b1fe9e1daa0718270ed5](https://etherscan.io/address/0x4ddc2d193948926d02f9b1fe9e1daa0718270ed5)

This will open the contract on EtherScan. Etherscan is an important tool you need to be aware of when operating on Ethereum. EtherScan is a way to look at all the data on Ethereum. All the contracts, all the transactions, all the function calls are all public on Ethereum and accessible from this website.

By default, the transactions tab is selected.

Here you can see all the transactions that have happened on Compound. Anyone who is depositing money, withdrawing or loaning is all publicly accessible here. 

You’ll also see a tab called Contract. Tap on that to see the contract itself. You’ll see the source code for CEther.sol.

Now that you have done Quest 1, you should be able to read what is going on in the contract. There are other contracts that are referenced by this contract using import statements on the top of the file, much like python. You’ll see all those sol files listed below CEther.sol as well. 

I don’t really want you to understand the logic of how Compound works yet. All that I want you to notice is that the code doesn’t look very different from the code that we’ve written already! A few hundred lines of Solidity code managing a few billions of dollars! Check out the balance of the top of the link on EtherScan :)

Here’s the only thing you need to know about how Compound works. You give ETH to Compound, it gives you cETH back. When you return the cETH to Compound, Compound gives you ETH back. The number of ETH you’ll get per cETH keeps increasing with time, because Compound is earning interest from the loans it’s giving out. So, the longer you wait, the more ETH you’ll get back for the same cETH you return to Compound. You can find more information on how compound works on their docs, but this is all that you need to know right now for this quest.
## Integrating with Compound
Go to the code we had written in our last quest.

[https://remix.ethereum.org/\#version=soljson-v0.8.4\+commit.c7e474f2.js&optimize=false&runs=200&gist=8e73b0872e4daff6b212c38646e715e6&evmVersion=null](https://remix.ethereum.org/#version=soljson-v0.8.4+commit.c7e474f2.js&optimize=false&runs=200&gist=8e73b0872e4daff6b212c38646e715e6&evmVersion=null)

First we will define what are the functions of Compound that we want to use.

We’ll do that by adding an interface. This interface defines what are the functions we’re going to use from Compound. We can reference only public functions from Compound’s contract. You can look at the source code for what mint() does on etherscan ([https://ropsten.etherscan.io/address/0x859e9d8a4edadfedb5a2ff311243af80f85a91b8\#code](https://ropsten.etherscan.io/address/0x859e9d8a4edadfedb5a2ff311243af80f85a91b8#code)). 

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/e174ecf9-07ff-4ffe-a88b-a51110b51b5d.jpg)

You will notice that, like we do in interfaces, we’re defining only the signatures of the functions we want to call. I’ve just copy pasted the signatures of the functions that we’ll use to deposit money, withdraw money and calculate our current interest that has been accrued.

Then we initialize the compound contract itself.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/8b893ca4-a1fe-448d-8aae-fc99d66600dc.jpg)

To initialize the contract we must create an object using the interface we created above and the address of the compound contract. 

By doing so, we’ve told Solidity as to what functions we will be using and which smart contract we’re targeting. 

Quick refresher, every smart contract has an account just like a user. Each of these accounts have an address by which they can be identified and can hold money. 

So, now we’re ready to start writing code using compound. 

But we still need to initialize the variable COMPOUND_CETH_ADDRESS. We already have the address of the cETH contract on Compound, but we’ll make a small change.

Let’s go back to [https://compound.finance/docs](https://compound.finance/docs)

Under networks, instead of choosing mainnet, choose ropsten.

Ropsten is a test network where we can play around with a few experiments before actually putting it on to the main network.

Copy the address against cETH under Ropsten and visit [https://ropsten.etherscan.io/address/0x859e9d8a4edadfedb5a2ff311243af80f85a91b8](https://ropsten.etherscan.io/address/0x859e9d8a4edadfedb5a2ff311243af80f85a91b8)PASTE ROPSTEN ADDRESS HERE

Notice this isn’t same as the link we had opened previously. This is what developers of compound have deployed for testing on Ropsten Test net.

Here on the top you’ll see a button to copy the address. This will be slightly different from what you had copied earlier on Compound Docs - few of the letters will be capitalized. For some reason the address mentioned on the compound website, at the time of this writing, has not mentioned the address in the correct format. Copy this correct address, set the variable COMPOUND_CETH_ADDRESS. 

You can use this ready made instead : 

[https://remix.ethereum.org/\#version=soljson-v0.8.4\+commit.c7e474f2.js&optimize=false&runs=200&gist=d40faac96075963e549444b1e101bd8c&evmVersion=null](https://remix.ethereum.org/#version=soljson-v0.8.4+commit.c7e474f2.js&optimize=false&runs=200&gist=d40faac96075963e549444b1e101bd8c&evmVersion=null)

We have of course not started using compound. That’s what we’ll do next.

## Depositing money to Compound
Let’s start using the functions that we’ve defined in the interface.

We’ll modify our addBalance method.

The way to deposit ethers to compound is to use the mint() function. 

If you notice the signature of this function in our interface, it is a payable function. That means we can send ethers to this function.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/25ab6c39-542a-4e91-b6a8-929daff52be7.jpg)

Note that we’re not only calling `ceth.mint()` but also sending ethers by setting the value for this function call by using `{ value : msg.value }`. 

This means that we will send all the ethers that the user sends to addBalance directly to compound.

Before we change the other two functions getBalance and withdraw, we need to understand how Compound works. 

When you deposit ETH using the `mint()` function, Compound generates some cETH for you. You can then give cETH to Compound and get back ETH.

Compound exposes how many cETH a certain user holds using the function balanceOf. It also exposes the number of ETH you can withdraw per cETH using exchangeRateStored. exchangeRateStored keeps steadily increasing, that’s how you earn interest on Compound. 1cETH keeps getting more valuable over time. If it returns 1ETH right now it will yield 1.00….01 ETH a couple minutes later.

We want to abstract the user from all these complications. They needn’t know that we’re using Compound internally to generate the interest. So we will take care of this calculation for them.

The current balance that a user can withdraw is the number of cETH they own multiplied by the exchange rate. Solidity doesn’t support floats and doubles. So exchangeRateStored is represented as an integer that must be divided by 10^18.

Let’s modify the getBalance function.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/437cace5-133e-412f-9e87-2ea49d0ea647.jpg)

Now let’s try to deploy this and see it in action.

`ceth.balanceOf()` gives us the number of cETH the user owns

`ceth.exchangeRateStored()` gives us the number of ETH we’ll get per cETH
## Deploying Compound Based Bank
[https://remix.ethereum.org/\#version=soljson-v0.8.4\+commit.c7e474f2.js&optimize=false&runs=200&gist=c2b6e762992683af636ef1430bdf7463](https://remix.ethereum.org/#version=soljson-v0.8.4+commit.c7e474f2.js&optimize=false&runs=200&gist=c2b6e762992683af636ef1430bdf7463)

So we’ve now added the compound integration. We should be able to deploy and check it out in action.

Compile, deploy and add balance as always.

This time you will see an error. Something like this

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/ccc405e9-672a-4472-ab10-361566f8a5a9.jpg)

This is because we are trying to access the compound’s contract cETH using the address we’ve hard coded. But this address exists only on the ropsten test net. To be able to access compound’s contract from our contract, we must also be deployed to the same network i.e. Ropsten.

Let’s see how to do that. But before that we need to get ready with a tool called metamask.

## Creating a real account w/ Metamask
In Remix, we do have a few toy accounts that are created for us with some toy money. It’s time for us to grow up. Let us create a real account.

There are many ways to do this, but we’ll go with the simplest and the most common way to do this.

Open up your web browser (chrome, firefox, edge) on your computer.

Head over to [https://metamask.io/download.html](https://metamask.io/download.html) and install the plugin/extension to your web browser.

After you install your metamask browser extension, you should see this button on your extensions area.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/dbbaa73c-d94c-4aa7-a6c8-002262a5975c.jpg)

After installation, you’ll also be redirected to initialize your metamask account. When asked, select “Yes, let’s setup” and tap create a wallet. A wallet is a collection of all your accounts.

Follow the onboarding steps and make sure you save your secret backup phrase somewhere safe.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/cf2609bc-0bef-400f-a566-e13288f92288.jpg)

Once you are all set up, and when you tap on the metamask extension, you’ll see something like this

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/4a5f11ca-47b1-47c0-a13a-1bba8df2c551.jpg)

This is your account. If you remember from the previous quest, an account is identified by an address that is mentioned right below the text “Account 1” and looks pretty much like the address that Remix had created for us “0x9234…”. That is now the address of your account. We also know that an account can hold money. Right now, you have 0Ethers in this account. Unlike Remix which gave you 100Eth, the real world gives you nothing. Harsh.

But yay, you’ve got your first real Ethereum account. 

Though you needn’t understand too much about cryptography to get going here, it might be useful to touch upon a few concepts here.

The seed phrase you were asked to save is the representation of your private key.

Every account has a public and a private key. To see this you can open the metamask extension and tap on the three dots and select account details. You can share your public key, that is the address of your account that we’ve been referring to for so long. The private key is to be kept secret only with you. The private key is used to generate a digital signature. When a document or a message is signed using a private key, there exist algorithms to find out the public key that is associated with the private key that signed it. Thereby, knowing who actually signed the document. Unlike a physical paper-pen signature, it is practically impossible to forge this digital signature.  

This account structure and digital signatures is how everything on Ethereum is secured. We will see how we leverage this security in just a bit.

## Adding money to your account, using a testnet
Now that you have an account, the first step is to add money into this account.

You can do this by buying ethers on platforms like Binance or Coinbase.

However, we’ll not have you spend money just yet. Ethereum also has a test net. Imagine it to be like a staging before production. It works exactly like the actual main-net Ethereum. Most contracts are usually first deployed to a testnet and only then moved to the mainnet.

We will be doing the same. To do so first go change the network on metamask. Open the plugin, tap on “Ethereum Mainnet” and select “Ropsten Testnet” instead.

Now let us add money to your account in this testnet. To encourage more people to develop contracts there are a few people who are giving away ethers for free that can be used only on the Ropsten network. They cannot be used on the mainnet.

To claim these testnet ethers head over to [https://moonborrow.com/](https://moonborrow.com/)

Login with your google account, copy the account address by opening your metamask and tapping on the account address. Paste it in the Ethereum address and hit claim.

In a few seconds your account will have ethers that you can use for playing with your contracts!

Check your metamask after about 20s.

## Back to deploying
Compile the file and head over to the transactions and deploy section from the left bar.

But this time instead of directly hitting deploy, we’ll change the environment.

The environment tells the Remix code editor whether to use the local toy Ethereum or an actual Ethereum Testnet or Mainnet.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/95ff90d1-549c-4116-a715-7ddf47553e6e.jpg)	

Choose injected web3. We will not go into why it is called injected right now, but will get back to it when we are developing the front end.

When you choose injected web3, your metamask will be opened automatically. Select the account and hit next.

Once you do, you will see your account added under the account section. You will also see that there is a balance associated with the account, same as what you see on metamask.

Now when we hit deploy, you will be using the real testnet ethers & metamask will ask you for a confirmation.

Your console log will show the following:

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/f317b043-0845-429b-8d0c-e72198dd979e.jpg)

 

Unlike the toy version, deployment here takes a minute or so.

You can check the current status by tapping on your metamask. It will show pending till it is actually deployed.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/c0b150b7-2ff2-4ef4-846d-6e28ec8553ce.jpg)

## calling compound functions
Now that the contract is deployed on ropsten, add value and execute add balance. This time add say 100 finney in value. 1000 finney = 1 eth.

After calling addBalance, let’s check the balance. 

Open your metamask and tap on your account address to copy it. 

Now paste that as the parameter to getBalance & hit getBalance

It will return 0. Oops! Why is that? Where did our money go?

Here is one more concept to understand. Compound doesn’t know who sent money to addBalance() on our contract. Someone sent money to our contract and our contract sent money to compound using ceth.mint{ value: msg.value }();

So all that compound sees is that money has come from the account of our contract. That means it will update the balance associated with the address of the account of our contract. Let’s see if that is true.

Copy the address of the smart contract you’ve just deployed



And paste that address and getBalance

You will see that the balance of our contract has been updated on compound. 

So, any money that the contract puts into compound starts attracting an interest.

So we need to calculate how much interest our users get so as to protect our users from the hassles of how compound works. All they need to know is they put in money, and they can withdraw their money safely and earn an interest.
## maintaining compound tokens state
Lets recap how compound works

When someone deposits money to compound using ceth.mint() compound takes ETH and allots cETHs to the user.

Then the user can deposit the cETH and claim the ETH back. 

So on our contract we need to maintain how many cETHs each user owns so that when that person decides to withdraw, we’re able to claim the appropriate amount from compound and transfer that to the user.

Compound’s mint() doesn’t return us the updated balance. So we need a work around.

We know that when we call the mint() function from our contract’s add balance, compound updates the number of cETH tokens owned by our contract. Compound has absolutely no knowledge of our users. So, we’ll calculate the number of tokens owned by the user of our contract by checking the difference between our contract’s balance of cETH before the mint() transaction and after. The difference is the number of cETH tokens that have been minted because of the user sending money to addBalance. We’ll update balances for that user’s cETH based on this difference.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/a5928f0f-f307-4312-8245-6963f5dde00f.jpg)

So now our balances\[\] mapping doesn’t contain the ETH balances, but rather cETH balances. 

So we need to rewrite our withdraw function to use cETHs and convert them into ETH so that our users never come to know anything about cETH. Ever.

Why don’t you try writing the withdraw function yourself? Feel free to check out the redeem function at [https://ropsten.etherscan.io/address/0x859e9d8a4edadfedb5a2ff311243af80f85a91b8\#code](https://ropsten.etherscan.io/address/0x859e9d8a4edadfedb5a2ff311243af80f85a91b8#code)

Also look at this documentation of Compound to see how to use Redeem, before we tell you how to use it : [https://compound.finance/docs/ctokens](https://compound.finance/docs/ctokens) 

Once done, check out how you did, here’s how to do it :

[https://remix.ethereum.org/\#version=soljson-v0.8.4\+commit.c7e474f2.js&optimize=false&runs=200&gist=c146acab1bd07fe94946d6a7fe00a894&evmVersion=null](https://remix.ethereum.org/#version=soljson-v0.8.4+commit.c7e474f2.js&optimize=false&runs=200&gist=c146acab1bd07fe94946d6a7fe00a894&evmVersion=null)


## next steps
Right now we’ve allowed for people to either deposit or withdraw once. What if i want to deposit in parts? Or withdraw in parts? Can you modify this contract to do that?

To be able to receive money while withdrawing you will need to define a `receive()` function in your contract so that Compound can send money to your contract and thereby to the user withdrawing. 

We leave this as an exercise for you to google and build yourself. 
Try it out and send us what you built!

Feel free to discuss on our [Discord Server](https://discord.gg/vhQmtMhCwX)

