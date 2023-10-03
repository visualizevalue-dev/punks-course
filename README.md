# Understanding the Punks Marketplace

## Intro

Us humans have always been collectors. We like to gather things, name things, and derive meaning from making that a shared experience with others.

In this little guide we want to explore the wild phenomenon of CryptoPunks together. Note – everything that's said is from our own personal lense & perspective, and doesn't necessarily represent the intent of the creators, owners, etc.. We'll go from a broad overview into the details of understanding actual code and core functionality.

Anyways, let's dive in!

## A High Level View

What in the world are CryptoPunks??!

![[./assets/intro.png]]

> 10,000 unique collectible characters 
> with proof of ownership stored on the Ethereum blockchain
> The project that inspired the modern CryptoArt movement. [...] 
> One of the earliest examples of a "Non-Fungible Token" [...].

Let's unpack.

### *1) 10,000 unique collectible characters*

Us humans have always been collectors. We like to gather things, name things, and derive meaning from making that a shared experience with others. Whether it's tangible things like gemstones or shells, watches or cars; less tangible things like books and art; or quite intangible things like ideas, knowledge, and insight. Collecting is what we do.
CryptoPunks just extend the scope in which we can collect items: The digital realm... Ten thousand 24×24 pixel portraits of punks, assembled from various individual traits like hairstyles and jewelry.

### *2) Proof of Ownership on the Ethereum Blockchain*

The Blockchain... Everyone has heard of it – "that thing where you can send digital money around". Well that's only a second order effect if you will. What the technology actually does is establish a shared log of historical events; a single source of truth of ***what*** happened ***when***.
This enables us to do much more than just send digital money back and forth – one of which is the tracking of ownership of any object over time.
CryptoPunks make use of this. When they launched, they came with code that processes changes in ownership of each individual punk. All changes have to go through this little program, which is immutable (meaning no one can change it) and unblockable (meaning no one can stop it). Anyone can prove who owns which punk, at any moment in time. 
The state of the Ethereum blockchain is validated by hundreds of thousands of computers, distributed around the entire globe. Anyone that wants to can participate in securing the network, and there is no entity that could come in to change any part of its state, unless they follow the rules set out by the blockchain itself, and these programs called "smart contracts".
For CryptoPunks, this piece of code is called the *"CryptoPunksMarket"* smart contract and we'll dive deep into it in a bit.

### *3) Inspired the modern CryptoArt movement [...]; One of the earliest examples of a "Non-Fungible Token"*

The worlds biggest artists, auction houses and brands are all in agreement: We live in a digitized world. This is just a new medium, like there have been new art forms in the past.. It's here to stay, and cryptography* and blockchain technologies enable us to collect, own, and trade them on a global, borderless market.
Each object that's kept track of is called a "token". Tokens are identifiers for these objects, like the serial number of a car or the ISBN number of a book. And more specifically, in the case of most art, we refer to them as "non fungible tokens", because each identifier points to a unique piece, which can't be exchanged with another one to one.
CryptoPunks were the seminal art project of its kind; and caught fire like no other. It inspired various technological standards that were derived from it and spearheaded an entire movement of established and new artists and developers experimenting in this new paradigm.
And with every day, as more people and institutions join this new world to build, experiment, and collect, this special station of CryptoPunks is further solidified. In the end, all roads lead back to punks...

Today, are exhibited in the most prestigious Museums like XXX, a cultural phenomenon celebrated by icons from fashion, music, art and technology.
To date, the CryptoPunksMarket smart contract has processed over 5 billion US$ in volume. Over XXX individual accounts have interacted with it, and over XXX accounts have owned punks. The most traded punk has changed hands XXX times.

All that, handled by this little piece of code that runs on Ethereum... If one removes whitespace and comments, it's less than 200 lines of code. 

5+ billion $ of secure trading. 
200 lines of code. 

Let that sink in...

A global community.
No middlemen or rent seekers.

Reflecting on this, it must become immediately clear, that this is a paradigm shift technology that has implications far beyond "just" digital art.

Let's have a glance over the contract and get a feel for how this code looks and what it does: [CryptoPunksMarket.sol](https://etherscan.deth.net/address/0xb47e3cd837dDF8e4c57F05d70Ab865de6e193BBB). The goal isn't to understand everything, but it's helpful to skim through something like this before going into details.
![[./assets/CryptoPunksMarketCode.png]]
## Actors & Actions

Something i like to do when looking at code, is making a model of it, in terms of "Subjects", "Objects", and "Verbs" or "Actions".

First, as the "subjects", we have people interacting with the smart contract. In Blockchain land, we call these "Accounts"\*\*.

If we map out the contract on a high level, we can identify:
- Subjects: `ContractOwner`, `PunkOwners`, `Bidders`
- Objects: `Punks`, `Bids`, `Offers`
- Actions: `assign`\*\*\*, `transfer`, `bid`, `acceptBid`, `withdrawBid`, `offerForSale`, `buy`, `withdrawEther`

Today, some of these aren't relevant anymore, especially because we need some more context to understand how punks were initially distributed. More on that later. For now, let's focus on the following:

Entities can...
- own punks
- transfer punks
- offer punks for sale
- buy punks
- make, withdraw, and accept bids on punks
- withdraw sale proceeds

## Code Primitives

If you're not familiar with code at all, there are a few basics we have to cover. Software code is a set of statements that tell the thing running the code what to do... There are many many different languages, but they all share some common characteristics. For us, we're looking at "Solidity" code, which is the most common programming language used to develop Smart Contracts on Ethereum. It compiles to low level machine code that runs on the Ethereum Virtual Machine ("EVM"). We can think of the EVM as a virtualized computer that makes sure EVM code runs exactly the same on all machines participating in the network.

### Variables and Data Types

The very first line in the market is this:

```solidity
imageHash = "ac39af4793119ee46bbff351d8cb6b5f23da60222126add4268e261199a2921b";
```

This is what we call a **variable assignment**. Variables are pointers to data we can refer to and play with within our programs. For this assignment specifically, if we try to find `imageHash` in any other place in the code, we see that's it's actually never changed. Hence we can actually call this a **constant**. The two prepending words `string`, and  `public` declare the type of data this constant points to (some text) and whether the contract exposes its value or keeps it private within the contract (this constant is public, meaning its value can be read by external accounts like us, or other smart contracts).
Even though it's not used anywhere else it's one of the most important lines in the entire contract. More on that in a bit. Let's continue looking at the next few lines...

```solidity
address owner;
```

This instantiates a variable of type `address` called `owner`, but doesn't assign any initial value to it. Every participant in the Ethereum network is identified by an `address`, which is a 40 hexadecimal character long string\*\*\*\*. Sidenote: This line refers to LarvaLabs themselves, the creators of the contract and will be set a bit further down during the "contract deployment" (the moment it's added to the Ethereum blockchain). This instantiation doesn't have the `public` declaration, so this variable is only accessible from within the contract code itself.

Jump a couple lines down:

```solidity
uint public nextPunkIndexToAssign = 0;
```

Here we assign the number zero (`0`) to a publicly accessible "unsigned (positive) integer" (`uint`) to the variable `nextPunkIndexToAssign`. The "punk index" is a number between 0 and 9,999 which identifies each individual punk.

Bit further down:

```solidity
mapping (uint => address) public punkIndexToAddress;
```

This is a special kind of data structure: `punkIndexToAddress` points to something not unlike an Excel table with two columns. The first column refers to the punk identifier between 0 and 9,999; and the second column contains the address of the owner of the respective punk:

XXX EXAMPLE TABLE

Next up let's look at an even more complex data structure:

```solidity
struct Offer {
	bool isForSale;
	uint punkIndex;
	address seller;
	uint minValue; // in ether
	address onlySellTo; // specify to sell only to a specific person
}

// A record of punks that are offered for sale at a specific minimum value, and perhaps to a specific person
mapping (uint => Offer) public punksOfferedForSale;
```

Here, instead of using a single simple type like a `string` or an `address`, we define our own custom data structure, an `Offer`. The offer is a collection of simple types, we pack into one bundle for easy access:
- `isForSale`: A boolean (`true`/`false`) stating whether the offer is valid or not.
- `punkIndex`: The identifier for the offered punk
- `seller`: The address of the account making the sell-offer
- `minValue`: The asked price of the punk
- `onlySellTo`: An optional address to make the offer exclusive to one buyer.

The mapping itself will look something like this:

XXX EXAMPLE TABLE

Next up, we have an interesting data type: "Events". With all kinds of computation happening onchain, we need a way to inform the outside world of relevant changes to our state, so that applications (like the CryptoPunks Website) can display what's happening on the blockchain accordingly.

`event PunkTransfer(address indexed from, address indexed to, uint256 punkIndex);`

The above event declaration reads as follows: "This contract can emit a `PunkTransfer` event with three data points attached: 1) `from` whom the punk was transferred (account address), `to` whom it was transferred, and which punk was transferred (via its 0-9,999 `punkIndex` identifier)". Later in the code we will see this event used whenever a punk is sold or sent from wallet to wallet.

### Statements



```solidity
owner = msg.sender;
totalSupply = 10000;
punksRemainingToAssign = totalSupply;
name = "CRYPTOPUNKS"; 
symbol = "Ͼ"; 
decimals = 0;
```


### Functions















---

\*) Participants don't have to trust each other, even in the presence of bad actors. Cryptography has enabled us to have private conversations online, and similarly its techniques can now be used to have asdkfja;lsdjf;asldjf;asldkfj

\*\*) Most blockchains use private-public key cryptography to enable self governed participation in the network. Key pairs controlled by entities (e.g. people) 

\*\*\*) The early story of CryptoPunks is actually split into two contracts: The initial claim, and the reassignment to the original claimers after a bug was found in the first version of the contract.

\*\*\*\*) Addresses are derived from... asdf;jsadfsaldkjhfalk


