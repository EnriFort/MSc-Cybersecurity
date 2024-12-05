# Blockchain and Distributed Ledgers Questions & Answers 📒🔗
---

[TOC]

## **Bitcoin Fundamentals Quiz**

### Q: Which of the following is true of SHA-256:
- It has been proven not to have a collision ❌
- We hope that there are no colisions ❌
- **No collision has ever been publicly found** ✅
- It has been proven that there is no fast way to find collisions ❌

**Explanation**

The fist one is false because collisions are theoretically possible for any cryptographic hash function due to the pigeonhole principle. 

The second is too vague and lacks the specificity of the known facts about SHA-256.

The last is not true, it doesn't exist a proof. For now what we only know is that no collision has been found. 


### Q: Which of the following types of modifications of a block chain data structure can be detected by someone who holds a hash pointer to the latest block?
- **Insertion of a block** ✅
- **Deletion of a block** ✅
- **Tempering of data in a block** ✅
- **Re-ordering of blocks** ✅

**Explanation**

We have a hash pointer, so any alteration to the blockchain — whether it involves inserting, deleting, tampering with, or reordering blocks — will invalidate the hash pointers, making the modification detectable.


### Q: Which of these keys are required for veryfing a signature?
- The secret key ❌
- **The public key** ✅
- Both the secret key and the public key ❌
- None. Keys are required only for signing; anyone can verify the signature without a key ❌

**Explanation**

To verify a signature, you need the public key of the signer. The private (secret) key is used only for creating the signature and remains confidential. Since the public key is meant to be shared, it allows anyone to verify the authenticity of the signature without requiring access to the private key.


### Q: If you generate numerous identities (public keys) for yourself and interact online using those different identities:

- **It is essential to have a good source of randomness. Otherwise adversaries might be able to deduce your secret key and take control of your identities** ✅
- Adversaries may be able to link your identities because public keys generated on the same computer tend to look similar ❌
- Adversaries may be able to de-anonymize you by analyzing your activity patterns ✅

**Explanation**

You need a good source of randomness because cryptographic key generation relies on randomness to ensure that secret keys are unpredictable. If the randomness is weak or predictable (e.g., using the same seed or predictable inputs), adversaries could deduce your private keys by analyzing patterns or using brute-force techniques. This would compromise all identities tied to those keys.

Additionally, adversaries may de-anonymize you by analyzing your activity patterns across different identities. For example, repeated behaviors, timestamps, or interactions could allow them to link seemingly separate identities.


### Q: Alice and Bob use ScroogeCoin. Alice owns ten coins, each under a different address (public key) and each of value 3.0. She would like to transfer coins of value 5.0 to Bob. Recall that the PayCoins transaction consumes (and destroys) some coins, and creates new coins of the same total value. Alice’s transfer will require, at a minimum:
- One PayCoins transaction, one new coin created, and one signature ❌ 
- **One PayCoins transaction, two new coins created, and two signatures** ✅
- Two PayCoins transaction, two new coins created, and four signatures ❌
- Two PayCoins transactions, one new coin created, and two signatures ❌


### Q: Which of these factors make distributed consensus hard?
- **Nodes may crash** ✅
- **Nodes may be taken over by malware** ✅
- Encrypted messages may be intercepted and decrypted ❌
- **There is latency on the network** ✅

**Explanation**

Crashing nodes is one of the main problem, because the network cannot determine the status of a crashed node. 

Nodes can also be Byzantine and send wrong information. 

The third  is generally not a direct factor in distributed consensus itself, but rather a security issue (e.g. using weak encryption algorithm). 

The latency is another problem: suppose a node crash and we have high latency. We can never know if the node is crashed or just have connection lag. 


### Q: Why is Bitcoin able to reach consensus in practice despite this being a generally difficult problem?
- Financial incentives cause participants to work togheter ✅
- Only small groups of nodes have to reach consensus rather than the network having to globally reach consensus ❌
- The order of blocks doesn't matter for consensus ❌
- Consensus has only to be reached over long times scales ✅

**Explanation**

Bitcoin does not actually solve all the problems of consensus, but it uses a tradeoff by incentivizing participants to behave honestly. 

Is not important when the final consensus is achieved, but what matters is that, eventually, all nodes reach a shared consensus. 

The order of block DOES MATTER for consensus because, over time, all nodes should have the same blockchain with identical information. 

There is a point in the future where all nodes should align on the same chain, ensuring consistency across the network.


### Q: What can a malicious node do?
- Create a valid transactions originating from someone else's address ❌
- Prevent a valid transaction from getting any confirmations ❌
- Ignore the longest valid branch rule when proposing a new block ✅

**Explanation**

For the first option, it is impossible for a malicious node to create a valid transaction originating from someone else's address unless they have access to the private key. A valid transaction must be signed with the private key of the address sending the transaction, ensuring its authenticity.

The malicious node can, however, ignore the longest valid branch rule when proposing a new block, potentially attempting to create a fork or disrupt the consensus process.


### Q: Proof of work is a way to:
- Select nodes in proportion to computing power ✅
- Let nodes compete for the "right" to create blocks ✅
- Make it impossible for one miner to act like many different miners ❌

**Explanation**

The more computing power you have, the higher the chances of finding the correct nonce (the solution to the proof of work) and winning the right to create the next block.

The last statement is not true because proof of work does not prevent a miner from simulating multiple miners. It is possible for a miner to create virtual or "fake" nodes, often called "sybil attacks," where the total computational power is still the same as the original machine. These virtual nodes would not change the total hash power of the system, but they could give the illusion of more independent miners participating.


### Q: A block in the block chain was found at time t. What is the probability that the next block was found at or before time t + 10 minutes? Assume that the total hash power of the network stays constant.
- More than 50% ✅
- Less than 50% ❌
- Exactly 50% ❌


### Q: A 51% attacker can potentially:
- Steal coins from an existing address ❌
- Make it unprofitable for others miners to mine ✅
- Change the block reward ❌
- Suppress transactions from the block chain ✅
- Spend two times the same coins ✅

**Explanation**

Stealing coins from an address requires the private key, not just control over the network. A 51% attacker can’t directly steal coins unless they have access to the private keys of the user’s wallet.

The second one is true because a 51% attacker has the ability to control the majority of the hash rate, which means they can create longer chains and rewrite the blockchain. This reduces the probability of other miners successfully finding blocks, making mining less profitable for them (loose trust on the coin &rarr; they sell the coin &rarr; the price drop). 

Block reward requires changing the protocol itself, which is a hard fork.

The 4th is true because a 51% attacker has the 51% of chance to be the next node that mine the next block, so it can select the transaction it wants.

The last one is true because with 51% control, an attacker can potentially reverse their own transactions, allowing them to spend the same coins again in a different transaction (double spending).


### Q: Which of the following are true?
- 51% attacks are difficult because an adversary would need to control more than half of the nodes on the Bitcoin network ❌
- Proof-of-work is essential for preventing sybil attacks on the Bitcoin blockchain ✅
- As a transaction gets buried deeper in the blockchain, it becomes less and less likely that it will ever be undone because the work required to make a longer alternate branch becomes more and more difficult ✅

**Explanation**

First is false, because a 51% attack requires controlling more than half of the computational power (hash rate) of the network, not the nodes. Mining pools, if they want, could amass enough hash rate to carry out such an attack. The reason they don’t is primarily due to the economic incentives; attacking the network would likely devalue their own holdings.

Tha last one is true for the **finality** property. 


### Q: In a typical transaction
- There is one signature that covers all the inputs ❌
- Each input contains a signature ✅
- There is one signature that covers all the outputs ❌
- Each output contains a signature ❌✅

**Explanation**

Of course the second one is true, we need to sign each input (from another point of view it correspond to sign each output).


### Q: Bitcoin’s script supports instructions whose effect is
- Adding two numbers ✅
- Conditional execution (if/then) ✅
- Looping ❌
- Recursion ❌
- Hashing ✅

**Explanation**

Script are NOT turing complete. In bitcoin script you cannot use loop, also everything you can do with loop you can also do with the recursion. 


### Q: Alice is paying for a service using Bitcoin micropayments. If she simply disconnects at some point without notifying Bob and stops sending micropayments, what can Bob do?
- Bob is out of luck. He doesn't earn any Bitcoins and must pursue legal recourse. ❌
- Bob can redeeem the maximum amount that Alice initially escrowed into a multisig address ❌
- Bob can redeem the latest micropayment transaction that Alice sent in the latest time period before disconnecting, which matches the length of service she received ✅
- Bob can refuse to sign the refund transaction, so both Alice and Bob will end up losing Bitcoins, which will sit in the multisig escrow forever ❌


### Q: Bitcoin micropayments require the use of:
- Multisignature Transactions ✅
- Proof of burn ❌
- Time-locked transactions ✅
- Pay-to-script-hash ❌

**Explanation**

Time lock is to ensure that Alice will not lose the money if bob start acting in a weird way. 


### Q: Blocks contain a tree of transactions instead of a flat list because
- It results in smaller blocks ❌
- It's easier to insert or delete new transactions while the block is being assembled ❌
- It enables efficiently proving that a transaction is included in a block ✅

**Explanation**

It is essentially the Merkle Tree.


### Q: If two conflicting transactions A → B and A → C are both broadcast almost simultaneously from different nodes, what determines which one will eventually end up in the block chain?
- The transaction that reaches the majority of nodes first will win ❌
- That transaction that was broadcast first will win ❌
- The miner who finds the next block will likely resolve the tie including one of the transaction in the block ✅
- Each node has its own version of the block chain containing the transaction that it heard about first ❌

**Explanation**

The first one is not true, because it is not about how many nodes a transaction reaches first, but rather about which transaction gets included in the next block. The miner who successfully mines the next block gets to choose which transactions to include (of course transaction fee helps in this). 

The last one is of course false because the blockchain is a **distributed ledger**. While nodes may temporarily have different views of the transaction pool, once a block is mined and added to the blockchain, all nodes will eventually reach consensus on the same version of the blockchain.


### Q: Which of the following requires a hard fork?
- Disabling the OP_SHA1 instruction ❌
- A requirement that each transaction have its outputs sorted by value in ascending (or non-decreasing) order ❌
- Increasing the maximum permitted size of blocks ✅
- Decreasing the maximum permitted size of blocks ✅

**Explanation**

The first one is false because disabling or modifying existing operations (like OP_SHA1) could be implemented via a soft fork. The same is for the second one. This type of rule change does not alter the validity of the transaction but enforces a new ordering convention.

Increasing the maximum block size requires changing the protocol in a way that older nodes would not recognize or accept the new, larger blocks. 
This would be a hard fork, as it is not backward-compatible. Nodes that haven't adopted the new rules would not be able to validate blocks larger than the previous maximum size.

---
## **Exam Q&A**

## Q: What are the key properties of a cryptographically secure hash function? (pag. 23)
A: The first cryptographic primitive that we’ll need as building blocks for the cryptocurrency is a cryptographic hash function. A **hash function** is a mathematical function with the following three properties:
- Its **input** can be any **string of any size**;
- It produces a **fixed size output**;
- It is **efficiently computable**.

Intuitively the last point means that for a given input string you can figure out what the output of the hash function is in a reasonable amount of time More technically computing the hash of an ​n​‐bit string should have a running time that is O(​n​).

Those properties define a general hash function while a **cryptographic hash functions**, to be cryptographically secure, has the following three additional properties: (1) **collision‐resistance**, (2) **hiding**, (3)  **puzzle‐friendliness**: 

1. **Collision-resistance**: A collision occurs when two distinct inputs produce the same output. A hash function ​H(.) ​is collision‐resistant if nobody can find a collision. Formally:

    > A hash function H is said to be collision resistant if it is infeasible to find two values, x and y, such that x≠y, yet H(x)=H(y).

    _Nobody can find a collision_ but the _the collision actually exist_. The input space to the hash function contains all strings of all lengths yet the output space contains only strings of a specific fixed length. Because the input space is larger than the output space (indeed, the 
    input space is infinite, while the output space is finite) there must be input strings that map to the same output string.

2. **Hiding**: The hiding property asserts that if we’re given the output of the hash function ​y ​= ​H(x)​, there’s no feasible way to figure out what the input, ​x​,was. This property can’t be true in the stated form. We must be slightly more precise about what we mean by hiding:

    >A hash function H is hiding if: when a secret value ​r ​is chosen from a probability distribution that has ​high min‐entropy​, then given ​H(r ‖ x) ​it is infeasible to find ​x​.

    **​Min‐entropy** ​​is a measure of how predictable an outcome is, and high  min‐entropy captures the intuitive idea that the distribution (i.e., random variable is very spread out). What that means specifically is that when we sample from the distribution there’s no particular value that’s likely to occur. So, for a concrete example, if ​r ​is chosen uniformly from among all of the strings that are 256 bits long then any particular string was chosen with probability 1/22​56​, which is an  infinitesimally small value. 

3. **Puzzle friendliness**: 
    
    > A hash function ​H ​is said to be puzzle‐friendly if for every possible n‐bit output value ​y​, if k is chosen from a distribution with high min‐entropy, then it is infeasible to find ​x ​such that H(k ‖ x) = y in time significantly less than ​2n​​.
    
    What this means is that if someone wants to target the hash function to come out to some particular output value ​y​, that if there’s part of the input that is chosen in a suitably randomized way, it’s very difficult to find another value that hits exactly that target. 
    
    


## Q: Explain the Merkle-Damgård construction. What issue does it address, and how does it work? (pag. 30)
A: The **Merkle-Damgård construction** is a technique used to create cryptographic hash functions that can handle inputs of any length while producing a fixed-size output. This approach is commonly used by hash functions like SHA-256, which is also used by Bitcoin due to its efficiency and security.

**Purpose and Problem Addressed**

The primary problem that the Merkle-Damgård construction addresses is how to design a hash function that can handle messages of arbitrary length while still producing a fixed-length hash output. Hash functions need to be able to take in any length of data and compress it into a manageable, consistent output. Without a structure like Merkle-Damgård, handling variable-length inputs securely and efficiently would be very challenging.

Additionally, Merkle-Damgård ensures that the hash function is **collision-resistant**—meaning it’s hard to find two different inputs that result in the same hash output—provided that the underlying **compression function** is also collision-resistant.

**How It Works**

1. **Message Padding**:
   - The input message is padded to a length that is a multiple of a fixed block size. Padding is usually done by adding a '1' bit, enough '0' bits to reach the necessary length, and the message’s original length as metadata at the end.
   
2. **Initialization Vector (IV)**:
   - The process starts with a predefined **Initialization Vector (IV)**. This fixed value serves as the initial input for the hash calculation and helps ensure that every hashing operation begins with the same consistent base.

3. **Compression Function**:
   - The Merkle-Damgård construction uses a **compression function** that takes a fixed-size input and produces a smaller, fixed-size output.
   - The message is split into fixed-size blocks (of length \(m - n\)) that match the input requirements of the compression function.

4. **Chaining and Iterative Processing**:
   - Each block is processed sequentially, where the output of one block is passed as input to the next block along with the next segment of the message. For each block, the input size is consistently \(m = (m - n) + n\), as required by the compression function.
   - The process continues, chaining each block’s output with the next, ensuring a smooth, secure, and collision-resistant flow across the message.

5. **Final Output**:
   - After processing all blocks, the output of the last block is the final hash of the entire message.

**Summary**

The ***Merkle-Damgård*** construction provides a systematic way to process any message length securely by chaining blocks of data through a collision-resistant compression function, starting with a fixed Initialization Vector. This ensures that the final output is both fixed in length and secure against common attacks, such as finding two inputs with the same hash.



## Q: Suppose you have a 2024-bit string and a hash function that uses a compression function with a 768-bit input and a 256-bit output. Into how many chunks should the string be divided, and what should be the length of each chunk?
A: To determine the number of chunks and the length of each chunk, let’s break down the details based on the Merkle-Damgård construction:

1. **Message Length**: We have a 2024-bit string.

2. **Compression Function Specifications**:
   - Input length of the compression function: 768 bits.
   - Output length of the compression function: 256 bits.

3. **Chunk Length Calculation**:
   - In the Merkle-Damgård construction, each chunk's length is set to match the difference between the compression function's input size and its output size.
   - Therefore, each chunk length should be:
     $$
     768 - 256 = 512 \text{ bits}
     $$

4. **Number of Chunks**:
   
   - To process the entire 2024-bit string with 512-bit chunks, we calculate the number of chunks as:
     $$
     
     \frac{2024}{512} = 3.953125
     $$
     
   - Since we cannot have a fractional chunk, we round up, so we’ll need **4 chunks** in total.
   
5. **Padding**:
   - To ensure we have exactly four 512-bit chunks, padding will be added to the last chunk.



**Answer**

- **The string should be divided into 4 chunks**.

- **Each chunk should be 512 bits** in length, with padding added to the last chunk to reach 512 bits exactly.

  


## Q: How does a digital signature scheme operate? Describe the two essential properties required for its security and effectiveness. (pag. 37)
A: **​Digital signatures​** is the second cryptographic primitive, along with hash functions, that is needed as building blocks for the cryptocurrency. A digital  signature is supposed to be the digital analog to a handwritten signature on paper. 

We desire **two properties** from digital signatures that correspond well to the handwritten signature analogy. 
1. Only you can make your signature, but anyone who sees it can verify that it’s valid. 

2. We want the signature to be tied to a particular document so that the signature cannot be used to indicate 
   your agreement or endorsement of a different document. 

For handwritten signatures, this latter property is analogous to assuring that somebody can’t take your signature and snip it off one document and glue it onto the bottom of another one.  



**Digital Signature Scheme**

A digital signature scheme consists of the following three algorithms:

- `(sk, pk) := generateKeys(keysize)`

  The `generateKeys` method takes a `key size` and generates a key pair. The secret key `sk` is kept privately and used to sign messages, while `pk` is the public verification key you give to everybody. Anyone with this key can verify your signature.

- `sig := sign(sk, message)`

  The `sign` method takes a message and a secret key,`sk`, as input and outputs a signature for `message` under `sk`.

- `isValid := verify(pk, message, sig)`

  The `verify` method takes a `message`, a signature, and a public key as input. It returns a boolean value, `isValid`, which will be **true** if `sig` is a valid signature for `message` under the public key `pk`, and **false** otherwise.

We require that the following two properties hold:

- **Valid signatures must verify** : `verify(pk, message, sign(sk, message)) == true`

- **Signatures are existentially unforgeable**

  

## Q: What are the economic incentives that drive participation in the Bitcoin network?
The economic incentives driving participation in the Bitcoin network primarily revolve around the **block rewards** and **transaction fees**. These incentives create a system where participants (such as miners, users, and investors) are motivated to contribute to the network's security, functionality, and growth. Below are the key economic incentives:

- **Block reward**: When a miner successfully mines a block, they include a special transaction which is a **coinbase transaction**. This **creates new coins** (the block reward) and pays them to a Bitcoin address of the miner’s choosing. Of course miners typically assign the reward to an address they control, ensuring they receive the payment. However, they could technically specify a different recipient address if they chose to do so. You can think of this as a payment to the node in exchange for the service of creating a block on the consensus chain. 

  Why the block reward incentivizes honest behavior? The node “collect” its 
  reward  only if the block in question ends up on the long‐term consensus branch 
  because just like every other transaction, the coin‐creation transaction will only be accepted by other nodes if it ends up on the consensus chain. That’s the key idea behind this incentive mechanism. It’s a very subtle but very powerful trick. It incentivizes nodes to behave in whatever way they believe will get other nodes to extend their blocks. So if most of the network is following the longest valid branch 
  rule, it incentivizes all nodes to continue to follow that rule. That’s Bitcoin’s first incentive mechanism.   	


- **Transaction fee**: Is the second incentive mechanism in Bitcoin. The creator of any transaction can choose to make the total value of the transaction outputs less than the total value of its inputs. Whoever creates the block that first puts that transaction into the block chain gets to 
  collect the difference, which acts a transaction fee. So if you’re a node that’s creating a block that contains, say, 200 transactions, then the sum of all those 200 transaction fees is paid to the address that you put into that block. The transaction fee is purely voluntary, but we expect, based on our 
  understanding of the system, that as the block reward starts to run out, it will become more and more important, almost mandatory, for users to include transaction fees in order to get a reasonable quality of service.

## Q: What is the block reward in Bitcoin? How has it changed over time, and what will happen to it in the future?

A: When a miner successfully mines a block, they include a special transaction which is a **coinbase transaction**. This **creates new coins** (the block reward) and pays them to a Bitcoin address of the miner’s choosing. Of course miners typically assign the reward to an address they control, ensuring they receive the payment. However, they could technically specify a different recipient address if they chose to do so. You can think of this as a payment to the node in exchange for the service of creating a block on the consensus chain.

The value of the block reward **halves every 210,000 blocks**. Based on the rate of block creation, this means that the rate **drops roughly every four years**.  For the first four years of Bitcoin’s existence, the block reward was 50 bitcoins; now it’s 3,125 bitcoins. And it’s going to keep halving. This has some interesting consequences for the future. The curve representing the halving is a geometric series and means that there is a finite amount. This is a total of 21 million bitcoins.

This is the only way in which new bitcoins are allowed to be created (coinbase transaction). There is no other coin generation mechanism, and that’s why 21 million is a final and total number (as the rules stand now, at least) for how many bitcoins there can ever be. This new block creation reward is actually going to run out in 2140, as things stand now. Does that mean that the system will stop working in 2140 and become insecure because nodes no longer have the incentive to behave honestly? Not quite, because there is another incentive mechanism: the transaction fee.

## Q: How does the proof-of-work mechanism function in Bitcoin?

A: The key idea behind **_proof‐of‐work_** is that we approximate the selection of a random node by instead selecting nodes in proportion to a resource that we hope that nobody can monopolize. If, for example, that resource is computing power, then 
it’s a proof‐of‐work system. Alternately, it could be in proportion to ownership of the currency, and that’s called proof‐of‐stake. 

Let’s try to get a better idea of what it means to select nodes in proportion to their computing power. Another way of understanding this is that we’re allowing nodes to compete with each other by using their computing power, and that will result in nodes automatically being picked in that proportion. Yet another view of proof‐of‐work is that we’re making it moderately hard to create new identities. It’s sort of a tax on identity creation and therefore on the Sybil attack. 

Bitcoin achieves proof‐of‐work using **_hash puzzles_**. In order to create a block, the node that proposes that block is required to find a number, or **nonce**, such that when you concatenate the nonce, the previous hash, and the list of transactions that comprise that block and take the hash of this whole string, then that hash output should be a number that falls into a target space that is quite small in relation to the much larger output space of that hash function. We can define such a target space as any value falling below a certain target value. In this case, the nonce will have to satisfy the following inequality: 
$$
H(\text{nonce} \, || \, \text{prev\_hash} \, || \, \text{tx} \, || \, \text{tx} \, || \, \dots \, || \, \text{tx}) < \text{target}
$$
As we saw earlier, normally a block contains a series of transactions that a node is proposing. In addition, a block also contains a hash pointer to the previous block. In addition, we’re now requiring that a block also contain a nonce. 

The idea is that we want to make it moderately difficult to find a nonce that satisfies this required property, which is that hashing the whole block together, including that nonce, is going to result in a particular type of output. If the hash function satisfies the puzzle‐friendliness property, then the only way to succeed in solving this hash puzzle is to just try enough nonces one by one until you get lucky. So specifically, if this target space were just one percent of the overall output space, you would have to try about 100 nonces before you got lucky. In reality, the size of this target space is not nearly as high as one percent of the output space. It’s much, much smaller than that. 

This notion of hash puzzles and proof of work completely does away with the requirement to magically pick a random node. Instead, nodes are simply independently competing to solve these hash puzzles all the time. Once in a while, one of them will get lucky and will find a random nonce that satisfies this property. That lucky node then gets to propose the next block. That’s how the system is completely decentralized. There is nobody deciding which node it is that gets to propose the next 
block.

## Q: What is a Coinbase transaction, and how does it differ from other transactions?

A: A **_coinbase transaction_** is a special transaction analogous to CreateCoins in Scroogecoin. This is where the creation of new coins in Bitcoin happens. It mostly looks like a normal transaction but with several differences: 

1. It always has a **single input** and a **single output**;
2. The **input doesn’t redeem a previous output** and thus contains a null hash pointer, since it is minting new bitcoins and not spending existing coins;
3. The value of the output is the miner’s revenue from the block. It consists of two components: a flat mining reward, which is set by the system and which halves every 210,000 blocks (about 4 years), and the transaction fees collected from every transaction included in the block;
4. There is a special “coinbase” parameter, which is completely arbitrary; miners can put whatever they want in there. One way in which the coinbase parameter has been used is to signal support by miners for different new features. 

## Q: How many decimal places does a Bitcoin have?

## Q: Define what a "Satoshi" is in the context of Bitcoin.

A: Bitcoin doesn’t have fixed denominations like US dollars, and in particular, there is no special designation of “1 bitcoin.” Bitcoins are just transaction outputs, and in the current rules, they can have an arbitrary value with 8 decimal places of precision. The smallest possible value is 0.00000001 BTC (bitcoins), which is called 1 **Satoshi**.

## Q: What is a 51% attack? Describe the types of attacks it enables and how such attacks could be executed.

A 51% attack would happen if consensus failed and there was a **51‐percent attacker** who controls 51 percent or more of the mining power in the Bitcoin network. There are two main possible attacks:

- Can the 51‐percent attacker suppress some transactions? Let’s say there is some user, Carol, whom the attacker really doesn’t like. The attacker knows some of Carol’s addresses, and wants to make sure that no coins belonging to any of those addresses can possibly be spent. 

  Since he controls the consensus process of the block chain, the attacker can simply refuse to create any new blocks that contain transactions from one of Carol’s addresses. The attacker can further refuse to build upon blocks that contain such transactions. However, he can’t prevent these transactions from being broadcast to the peer‐to‐peer network because the network doesn’t depend on the block chain, or on consensus, and we’re assuming that the attacker doesn’t fully control the network. The attacker cannot stop the transactions from reaching the majority of nodes, so even if the attack succeeds, it will at least be apparent that the attack is happening. 

- Can the attacker somehow destroy confidence in Bitcoin? Well, let’s imagine what would happen. If there were a variety of double‐spend attempts, situations in which nodes did not extend the longest valid branch, and other attempted attacks, then people are going to likely decide that Bitcoin is no longer acting as a decentralized ledger that they can trust. People will lose confidence in the currency, and we might expect that the exchange rate of Bitcoin will plummet. In fact, if it is known that there is a party that controls 51 percent of the hash power, then it’s possible that people will lose confidence in Bitcoin even if the attacker is not necessarily trying to launch any attacks. 

  So it is not only possible, but in fact likely, that a 51 percent attacker of any sort will destroy confidence in the currency. Indeed, this is the main practical threat if a 51 percent attack were ever to materialize. Considering the amount of expenditure that the adversary would have to put into attacking Bitcoin and achieving a 51 percent majority, none of the other attacks that we described really make sense from a financial point of view.  