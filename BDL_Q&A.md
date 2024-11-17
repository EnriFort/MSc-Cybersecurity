# Blockchain and Distributed Ledgers Questions & Answers 📒🔗

## Q: What are the key properties of a cryptographically secure hash function? (pag. 23)

A: The first cryptographic primitive that we’ll need to understand is a **​cryptographic hash function**. ​A **hash function** ​is a mathematical function with the following three properties:
- Its **input** can be any **string of any size**
- It produces a **fixed size output**
- It is **efficiently computable**
Intuitively the last point means that for a given input string you can figure out what the output of the hash function is in a reasonable amount of time More technically computing the hash of an ​n​‐bit string should have a running time that is O(​n​).

Those properties define a general hash function while a _​cryptographic_ ​hash functions, to be cryptographically secure, has the following three additional 
properties: (1) **collision‐resistance**, (2) **hiding**, (3)  **puzzle‐friendliness**: 
1. **Collision-resistance**: A collision occurs when two distinct inputs produce the same output. A hash function ​H(.) ​is collision‐resistant if nobody can find a collision. Formally:

    > ​A hash function ​H ​is said to be collision resistant if it is infeasible to find​​ two values, ​x ​and ​y​, such that ​x​≠​​y​, yet ​H(x)​=​H(y)​.

    _Nobody can find a collision_ but the _the collision actually exist_. The input space to the hash function contains all strings of all lengths yet the output space contains only strings of a specific fixed length. Because the input space is larger than the output space (indeed, the 
    input space is infinite, while the output space is finite) there must be input strings that map to the same output string.

2. **Hiding**: The hiding property asserts that if we’re given the output of the hash function ​y ​= ​H(x)​, there’s no feasible way to figure out what the input, ​x​,was. This property can’t be true in the stated form. We must be slightly more precise about what we mean by hiding:

    >A hash function H is hiding if: when a secret value ​r ​is chosen from a probability distribution that has ​high min‐entropy​, then given ​H(r ‖ x) ​it is infeasible to find ​x​.

    **​Min‐entropy** ​​is a measure of how predictable an outcome is, and high  min‐entropy captures the intuitive idea that the distribution (i.e., random variable is very spread out). What that means specifically is that when we sample from the distribution there’s no particular value that’s likely to occur. So, for a concrete example, if ​r ​is chosen uniformly from among all of the strings that are 256 bits long then any particular string was chosen with probability 1/22​56​, which is an  infinitesimally small value. 

3. **Puzzle friendliness**: 
    > A hash function ​H ​is said to be puzzle‐friendly if for every possible n‐bit output value ​y​, if k is chosen from a distribution with high min‐entropy, then it is infeasible to find ​x ​such that H(k ‖ x) = y in time significantly less than ​2n​​.

    What this means is that if someone wants to target the hash function to come out to some particular output value ​y​, that if there’s part of the input that is chosen in a suitably randomized way, it’s very difficult to find another value that hits exactly that target. 


## Q: Explain the Merkle-Damgård construction. What issue does it address, and how does it work? (pag. 30)
The **Merkle-Damgård construction** is a technique used to create cryptographic hash functions that can handle inputs of any length while producing a fixed-size output. This approach is commonly used by hash functions like SHA-256, which is also used by Bitcoin due to its efficiency and security.

### Purpose and Problem Addressed
The primary problem that the Merkle-Damgård construction addresses is how to design a hash function that can handle messages of arbitrary length while still producing a fixed-length hash output. Hash functions need to be able to take in any length of data and compress it into a manageable, consistent output. Without a structure like Merkle-Damgård, handling variable-length inputs securely and efficiently would be very challenging.

Additionally, Merkle-Damgård ensures that the hash function is **collision-resistant**—meaning it’s hard to find two different inputs that result in the same hash output—provided that the underlying **compression function** is also collision-resistant.

### How It Works
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

### Summary
The Merkle-Damgård construction provides a systematic way to process any message length securely by chaining blocks of data through a collision-resistant compression function, starting with a fixed Initialization Vector. This ensures that the final output is both fixed in length and secure against common attacks, such as finding two inputs with the same hash.



## Q: Suppose you have a 2024-bit string and a hash function that uses a compression function with a 768-bit input and a 256-bit output. Into how many chunks should the string be divided, and what should be the length of each chunk?
To determine the number of chunks and the length of each chunk, let’s break down the details based on the Merkle-Damgård construction:

1. **Message Length**: We have a 2024-bit string.

2. **Compression Function Specifications**:
   - Input length of the compression function: 768 bits.
   - Output length of the compression function: 256 bits.

3. **Chunk Length Calculation**:
   - In the Merkle-Damgård construction, each chunk's length is set to match the difference between the compression function's input size and its output size.
   - Therefore, each chunk length should be:
     \[
     768 - 256 = 512 \text{ bits}
     \]

4. **Number of Chunks**:
   - To process the entire 2024-bit string with 512-bit chunks, we calculate the number of chunks as:
     \[
     \frac{2024}{512} = 3.953125
     \]
   - Since we cannot have a fractional chunk, we round up, so we’ll need **4 chunks** in total.

5. **Padding**:
   - To ensure we have exactly four 512-bit chunks, padding will be added to the last chunk.

### Answer
- **The string should be divided into 4 chunks**.
- **Each chunk should be 512 bits** in length, with padding added to the last chunk to reach 512 bits exactly.


## Q: How does a digital signature scheme operate? Describe the two essential properties required for its security and effectiveness. (pag. 37)
**​Digital signatures​** is the second cryptographic primitive, along with 
hash functions, that we need as building blocks for the cryptocurrency. A digital  signature is supposed to be the digital analog to a handwritten signature on paper. 

We desire **two properties** from digital signatures that correspond well to the handwritten signature analogy. Firstly, only you can make your signature, but anyone who sees it can verify that it’s valid. Secondly, we want  the signature to be tied to a particular document so that the signature cannot be used to indicate 
your agreement or endorsement of a different document. For handwritten signatures, this latter 
property is analogous to assuring that somebody can’t take your signature and snip it off one 
document and glue it onto the bottom of another one.  


> ### Digital Signature Scheme
> A digital signature scheme consists of the following three algorithms:
> 
> - **(sk, pk) := generateKeys(keysize)**  
>   The `generateKeys` method takes a key size and generates a key pair. The secret key **sk** is kept privately and used to sign messages, while **pk** is the public verification key you give to everybody. Anyone with this key can verify your signature.
> 
> - **sig := sign(sk, message)**  
>   The `sign` method takes a message and a secret key, **sk**, as input and outputs a signature for **message** under **sk**.
> 
> - **isValid := verify(pk, message, sig)**  
>   The `verify` method takes a message, a signature, and a public key as input. It returns a boolean value, **isValid**, which will be **true** if **sig** is a valid signature for **message** under the public key **pk**, and **false** otherwise.
> 
> We require that the following two properties hold:
> 
> - **Valid signatures must verify**  
>   `verify(pk, message, sign(sk, message)) == true`
> 
> - **Signatures are existentially unforgeable**



## Q: What are the economic incentives that drive participation in the Bitcoin network?
The economic incentives driving participation in the Bitcoin network primarily revolve around the **block rewards** and **transaction fees**. These incentives create a system where participants (such as miners, users, and investors) are motivated to contribute to the network's security, functionality, and growth. Below are the key economic incentives:

- **Block reward**: 




- **Transaction fee**:

## Q: What is the block reward in Bitcoin? How has it changed over time, and what will happen to it in the future?

## Q: How does the proof-of-work mechanism function in Bitcoin?

## Q: What is a Coinbase transaction, and how does it differ from other transactions?

## Q: How many decimal places does a Bitcoin have?

## Q: Define what a "Satoshi" is in the context of Bitcoin.

## Q: What is a 51% attack? Describe the types of attacks it enables and how such attacks could be executed.