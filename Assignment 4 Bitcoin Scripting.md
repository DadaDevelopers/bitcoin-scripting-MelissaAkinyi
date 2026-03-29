# **Assignment 4: Bitcoin Scripting**



##### Overview

This assignment covers Bitcoin scripting, including a standard Pay-to-Public-Key-Hash (P2PKH) script and a Hashed Time-Lock Contract (HTLC) for atomic swaps. The goal is to understand Bitcoin's stack-based scripting language and conditional spending.





## Assignment A: P2PKH Script



##### Script

```bitcoin

OP\_DUP OP\_HASH160 <PubKeyHash> OP\_EQUALVERIFY OP\_CHECKSIG



##### Opcode Breakdown

OP\_DUP

Duplicates the public key on the stack so it can be used twice.



OP\_HASH160

Hashes the duplicated public key using SHA256 followed by RIPEMD160.



<PubKeyHash>

Pushes the expected public key hash onto the stack for comparison.



OP\_EQUALVERIFY

Compares the computed hash and expected hash.

If they are not equal → script fails immediately

If equal → execution continues



OP\_CHECKSIG

Verifies that the signature matches the public key.

Valid → TRUE

Invalid → FALSE



##### Data Flow Diagram

Unlocking Script (scriptSig):

<signature> <public key>



#### Execution:



\[signature, pubKey]

→ OP\_DUP

\[signature, pubKey, pubKey]



→ OP\_HASH160

\[signature, pubKey, hash(pubKey)]



→ Push <PubKeyHash>

\[signature, pubKey, hash(pubKey), expectedHash]



→ OP\_EQUALVERIFY

\[signature, pubKey]



→ OP\_CHECKSIG

\[TRUE / FALSE]



What Happens if Signature Verification Fails?

OP\_CHECKSIG fails → script returns FALSE

Transaction is invalid

Coins cannot be spent

##### 

##### Security Benefits of Hash Verification

Privacy: Public key is hidden until spending

Compact representation: Hash is shorter than full key

Security against key exposure: Public key revealed only when spending

Protection against substitution attacks: Must match hash exactly



## Assignment B: HTLC (Hashed Time-Lock Contract)

##### HTLC Script

OP\_IF

&#x20;   OP\_HASH160 <Hash160(secret)> OP\_EQUALVERIFY

&#x20;   <AlicePubKey> OP\_CHECKSIG

OP\_ELSE

&#x20;   <timeout> OP\_CHECKLOCKTIMEVERIFY OP\_DROP

&#x20;   <BobPubKey> OP\_CHECKSIG

OP\_ENDIF



Claiming Transaction Script (Alice claims with secret)

<signature\_Alice> <secret> OP\_TRUE



OP\_TRUE selects the IF branch

Secret is hashed and verified

Alice’s signature is checked

Funds are released to Alice



Refund Transaction Script (Bob refunds after timeout)

<signature\_Bob> OP\_FALSE



OP\_FALSE selects the ELSE branch

OP\_CHECKLOCKTIMEVERIFY ensures timeout has passed

Bob provides signature to reclaim funds



#### Sample Test (Hash and Timeout)



Secret: mysecret123



Hash (Hash160): RIPEMD160(SHA256("mysecret123")) = <sample hash>



Timeout:



Bitcoin uses block height

1 block ≈ 10 minutes

21 minutes ≈ 2 blocks

Example:



Current block height: 840000

Timeout: 840002

