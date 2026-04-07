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


ASSIGNMENT B

HTLC SCRIPT
OP_IF
    OP_HASH160 <your_hash_here> OP_EQUALVERIFY
    <Alice_pubkey> OP_CHECKSIG
OP_ELSE
    <locktime> OP_CHECKLOCKTIMEVERIFY OP_DROP
    <Bob_pubkey> OP_CHECKSIG
OP_ENDIF

CLAIM SCRIPT
<Alice_signature> <Alice_pubkey> <secret> OP_TRUE

REFUND SCRIPT
<Bob_signature> <Bob_pubkey> OP_FALSE

This HTLC allows Alice to claim funds by revealing the correct preimage before the timeout. If she fails, Bob can reclaim the funds after the locktime using OP_CHECKLOCKTIMEVERIFY. The script ensures atomicity and trustless exchange.















