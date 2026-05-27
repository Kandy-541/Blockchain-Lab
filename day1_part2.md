# Day 1 — Session 2: Blockchain Fundamentals & Cryptography
**Lead Developer:** cjayy  
**Section:** Session 2 — Hash Exploration & Consensus Discussion  

---

## 2.1 Hash Exploration

### hash_demo.py — Code

```python
import hashlib
import json

def sha256(data: str) -> str:
    return hashlib.sha256(data.encode()).hexdigest()

# --- Experiment 1: Single character change ---
msg1 = "Blockchain is secure"
msg2 = "blockchain is secure"  # lowercase 'b'

print("Hash 1:", sha256(msg1))
print("Hash 2:", sha256(msg2))
print("Same? ", sha256(msg1) == sha256(msg2))

# --- Experiment 2: Simulate a block ---
block = {
    "index": 1,
    "data": "Alice pays Bob 5 ETH",
    "previous_hash": "0" * 64
}
block_string = json.dumps(block, sort_keys=True)
print("\nBlock Hash:", sha256(block_string))
```

### hash_demo.py — Output

```
Hash 1: de6411a3dd61522b5c6d536fa92723958973bd33fa28c74b2d6df411c0a4c4e4
Hash 2: 3cf9b98da9d7b02cf5b0cae2b49a262680d00494cf621fd2a3536ee320d5c36e
Same?  False

Block Hash: 221a140c1dd664d469e71b0ecb21a0a11734b254f27404de1e26350894203601
```

### Observation — Avalanche Effect
A single character change (uppercase `B` → lowercase `b`) produces a completely different 64-character hash. This property is called the **Avalanche Effect** — it means tampering with any data in a block is immediately detectable by the network, since even the tiniest change produces an entirely different hash.

---

## 2.2 Proof of Work Simulation

### pow_demo.py — Code

```python
import hashlib, time

def mine_block(data: str, difficulty: int) -> dict:
    prefix = "0" * difficulty
    nonce = 0
    start = time.time()
    while True:
        candidate = f"{data}{nonce}"
        h = hashlib.sha256(candidate.encode()).hexdigest()
        if h.startswith(prefix):
            elapsed = time.time() - start
            return {"nonce": nonce, "hash": h, "time": elapsed}
        nonce += 1

# Run difficulty 3
print("--- Difficulty 3 ---")
result = mine_block("SSBlock#1", difficulty=3)
print(f"Nonce: {result['nonce']}")
print(f"Hash:  {result['hash']}")
print(f"Time:  {result['time']:.3f}s")

# Run difficulty 5
print("\n--- Difficulty 5 ---")
result = mine_block("SSBlock#1", difficulty=5)
print(f"Nonce: {result['nonce']}")
print(f"Hash:  {result['hash']}")
print(f"Time:  {result['time']:.3f}s")
```

### pow_demo.py — Output

```
--- Difficulty 3 ---
Nonce: 1120
Hash:  000147710b0b95a73090bc80ef6b065eb3b79adee85bfcaaae5e5cca39cc2014
Time:  0.005s

--- Difficulty 5 ---
Nonce: 273175
Hash:  000004ab82f1ccda4f6b2e86b568bdd372fad2084facb6cc62479d043ff5fee5
Time:  0.313s
```

### Observation — Difficulty Comparison

| Difficulty | Nonces Tried | Time Taken |
|---|---|---|
| 3 (prefix: `000`) | 1,120 | 0.005s |
| 5 (prefix: `00000`) | 273,175 | 0.313s |

Increasing difficulty from 3 to 5 required **243x more nonce attempts** and took **62x longer**. This demonstrates how miners on real networks expend significant computational resources to find a valid hash — making it economically infeasible to rewrite blockchain history, since an attacker would need to redo all that work.

---

## 2.3 Consensus Mechanisms — Group Discussion

### Q1: What is the primary security tradeoff between Proof of Work and Proof of Stake?

Proof of Work requires enormous computational energy to attack, making it extremely costly for a malicious actor to rewrite the chain. However, this comes at the expense of significant electricity consumption. Proof of Stake is far more energy efficient, but security depends on economic stake — a sufficiently wealthy attacker could potentially acquire enough staked tokens to influence consensus decisions on the network.

---

### Q2: In a 5-node network, how many nodes must be compromised for a 51% attack?

At least **3 nodes** must be compromised. Since 3 out of 5 represents 60% of the network, this exceeds the 51% threshold required to gain majority control over consensus. At that point the attacker could override legitimate transactions and potentially double-spend.

---

### Q3: Why is finality important for financial transactions on a blockchain?

Finality means that once a transaction is confirmed, it cannot be reversed or altered. Without finality, a merchant could receive a payment that is later rolled back by an attacker — a technique known as a double-spend attack. Financial systems require absolute certainty that settled transactions are permanent, which is why finality is a critical security property in any blockchain used for payments or asset transfers.

---

## 2.4 Block Anatomy — Fields Reference
*(Scribe to draw and label a block diagram using the fields below)*

| Field | Description |
|---|---|
| **Block Index** | Sequential number identifying the block's position in the chain. Genesis block = 0 |
| **Timestamp** | Unix epoch time of block creation, establishing chronological order |
| **Transaction Data** | The payload — financial transactions, contract calls, or arbitrary data |
| **Previous Hash** | SHA-256 of the preceding block's header, creating the immutable chain link |
| **Nonce** | Number incremented during mining until the resulting hash meets the difficulty target |
