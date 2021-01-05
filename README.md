# ERC1155 Auction Contract on Ethereum

This smart contract is a basic implementation of an English auction for an ERC1155 contract. 

The ERC1155 smart contract standard is used to host several fungible and non-fungible tokens at the same time in a class based contract. Each class of token must be identified by an uint256 ID, which is sent to the contract to identify the token being specified. This ERC1155 auction contract relies on the expert auditing of OpenZeppelin and extends their work with custom functions to enable an auction of the tokens within the ERC1155 contract to take place.

The image below describes the interactions of stakeholders with the contract.

![](<./Ethereum ERC1155 Auction Diagram.jpg>)

<br />

# Functions: 

In its current state, the auction contract adds two new functions to the ERC1155 contract: <br />


**Bid() Function:** <br />
The first extension is the **bid() function**, which allows players to place one bid for one item by sending an
Ether transaction to the smart contract with an id for the bid function to identify the item they are bidding
on. The bid() function can only be executed by outside parties before the end time of the auction. The
bid() function enforces this restriction using the “require()” function in Solidity. If the auction end time
(AUCTION_END_TIME) is smaller than the current block timestamp, then their bid is sent back to the
sending address. The bidder incurs the gas fee. Moreover, the contract will throw an error and revert its
last actions if the item “id” passed in the transaction is not within the range of valid ids, the message value
is below the initial item price set by the auction owner, or if a bid exists for all available items and the
new bid is lower than the lowest bid. If none of the requirements are breached, the bid is accepted. <br />

Since an ERC1155 contract can hold several non-fungible tokens of the same class, the bidding for these
tokens must be done in a way that allows the auction smart contract to sell all available tokens to bidders.
In this conceptual design, a sorted list of all bids is created, which has a length equal to the amount of the
specific item being auctioned. If there are less bids than items available, the new bid is appended to a
sorted linked list of bids. If there are more bids than items, a for loop starts at the least expensive bid in
the sorted linked list of bids and places the new bid into the sorted list, while popping off the lowest. This
allows for a quasi English auction to take place within the contract, where the highest bidders win the
items. Bidders, whose bids get overbid, get their money back, but pay the gas fee. This
incentivises high bids from the start. The drawback of this method is the fact that if many items are being
auctioned, the reordering of the list will be very expensive in terms of gas and might deter bidders.

**Distribute Function:** <br />
The second extension to the ERC1155 contract is the distribute() function, which is used to distribute the
tokens to the auction winners and the funds raised to the game developer after the auction has ended. The
function can be only called after the auction. This requirement is enforced by using the “require()”
function in Solidity to throw an error, if the bid() function is called while the auction’s end time
(AUCTION_END_TIME) is smaller than the current block timestamp (block.timestamp). To make sure
that the function is not called twice, another require() call checks if a bool value has been flipped yet. The
distribute() function can be called by the owner of the auction or any other party that is willing to pay the
high gas fee to distribute all tokens to the auction winners and send the owner his raised funds (including
gas fee). This allows the players to pool their funds and activate the contract execution if the owner
refuses to do so himself or is unable to (e.g. after losing private key or his life).


**Linked List Data Structure used in the Contract:** <br />

![](<./Linked list diagram step 1.png>) <br />

![](<./Linked list diagram step 2.png>)


# Helpful Links:

This contract extends the ERC1155 contract published and audited by OpenZeppelin. The documentation of which can be found here: <br />
https://docs.openzeppelin.com/contracts/3.x/erc1155

If you do not know how Solidity works, please read this great resource on the topic: <br />
https://docs.soliditylang.org/en/v0.8.0/

And this resource on Ethereum: <br />
https://ethereum.org/en/whitepaper/

