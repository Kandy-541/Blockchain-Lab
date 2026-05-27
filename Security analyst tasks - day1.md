Security analyst tasks - day1

Lead session 2 with the hashing excercises

-Writing and running hash_demo.py (the SHA-256 avalanche effect demo)

-Writing and running pow_demo.py (the proof of work mining simulation)

-Leading the group discussion on consensus mechanisms (Q1, Q2, Q3)

Check Marlyse documentation for the outputs, codes(hash_demo.py and pow_demo.py) or check day1 report part 2

or, let me just do it.

bash```
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

This is hash_demo.py. after running it, the output was this:

```bash
cjayy@cjayy:~/blockchain-lab$ python3 hash_demo.py
Hash 1: de6411a3dd61522b5c6d536fa92723958973bd33fa28c74b2d6df411c0a4c4e4
Hash 2: 3cf9b98da9d7b02cf5b0cae2b49a262680d00494cf621fd2a3536ee320d5c36e
Same?  False

Block Hash: 221a140c1dd664d469e71b0ecb21a0a11734b254f27404de1e26350894203601
```

Next is pow_demo.py

```bash
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

After running it, the output was this:

```bash
cjayy@cjayy:~/blockchain-lab$ python3 pow_demo.py
--- Difficulty 3 ---
Nonce: 1120
Hash:  000147710b0b95a73090bc80ef6b065eb3b79adee85bfcaaae5e5cca39cc2014
Time:  0.005s

--- Difficulty 5 ---
Nonce: 273175
Hash:  000004ab82f1ccda4f6b2e86b568bdd372fad2084facb6cc62479d043ff5fee5
Time:  0.313s
```

In fact, the whole day1_part2 document is yours, this is just and overview, so go over there and review everything, read the documentation too to better understand what and why every code was ran.