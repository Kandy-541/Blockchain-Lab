# Day 1 — Scribe Handoff Document
**Lead Developer:** cjayy  
**Date:** Day 1  
**Contract:** SimpleStorage.sol  

---

## 1. Contract Address

```
0xe78A0F7E598Cc8b0Bb87894B0F60dD2a88d6a8Ab
```

---

## 2. SimpleStorage.sol Code

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/// @title SimpleStorage - Lab Day 1 Starter Contract
/// @notice Demonstrates state variables, events, and access control
contract SimpleStorage {
    uint256 private storedValue;
    address public owner;

    event ValueUpdated(address indexed updater, uint256 newValue);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorised");
        _;
    }

    function setValue(uint256 _val) public onlyOwner {
        storedValue = _val;
        emit ValueUpdated(msg.sender, _val);
    }

    function getValue() public view returns (uint256) {
        return storedValue;
    }
}
```

---

## 3. Deploy Script — scripts/deploy.js

```javascript
const { ethers } = require("hardhat");

async function main() {
    const Storage = await ethers.getContractFactory("SimpleStorage");
    const contract = await Storage.deploy();
    await contract.waitForDeployment();
    console.log("Contract deployed to:", await contract.getAddress());
}

main().catch((err) => { console.error(err); process.exit(1); });
```

---

## 4. Terminal Output — Compile & Deploy

**Compile:**
```
Compiled 1 Solidity file successfully
```

**Deploy:**
```
Contract deployed to: 0xe78A0F7E598Cc8b0Bb87894B0F60dD2a88d6a8Ab
```

---

## 5. Full Console Interaction Output

```javascript
> const Storage = await ethers.getContractFactory("SimpleStorage");
undefined
> const contract = await Storage.attach("0xe78A0F7E598Cc8b0Bb87894B0F60dD2a88d6a8Ab");
undefined
> await contract.setValue(42);
ContractTransactionResponse {
  blockNumber: 2,
  blockHash: '0x4205230be2b569cd8e29f4b822a7e974b62de7748cfcc0dd6c21a8212a71d502',
  hash: '0x0c34384350d5f73ab34b1cea6cc4e6c404949a5a02678caf01b0c42d162c5fa5',
  from: '0x90F8bf6A479f320ead074411a4B0e7944Ea8c9C1',
  to: '0xe78A0F7E598Cc8b0Bb87894B0F60dD2a88d6a8Ab',
  nonce: 1,
  gasLimit: 47468n,
  gasPrice: 971396323n,
  type: 2,
  chainId: 1337n,
  ...
}
> const val = await contract.getValue();
undefined
> console.log(val.toString());
42
undefined
> const [owner, notOwner] = await ethers.getSigners();
undefined
> await contract.connect(notOwner).setValue(99);
Uncaught ProviderError: VM Exception while processing transaction: revert Not authorised
```

---

## 6. Explanation of the Error

> When a non-owner account (index 1, address `0xFFcf8FDEE72ac11b5c542428B35EEF5769C409f0`) attempted to call `setValue(99)`, the transaction was rejected with the error: `revert Not authorised`. This is a security feature — the `onlyOwner` modifier in `SimpleStorage.sol` checks that `msg.sender` matches the owner address set during deployment. Since the deployer was account index 0, any other account is blocked from modifying state. This demonstrates that access control is correctly enforced at the smart contract level.

---

## 7. Ganache Account List

```
ganache v7.9.2 (@ganache/cli: 0.10.2, @ganache/core: 0.10.2)

Starting RPC server

Available Accounts
==================
(0) 0x90F8bf6A479f320ead074411a4B0e7944Ea8c9C1 (1000 ETH)  ← Owner / Deployer
(1) 0xFFcf8FDEE72ac11b5c542428B35EEF5769C409f0 (1000 ETH)  ← notOwner (used in access control test)
(2) 0x22d491Bde2303f2f43325b2108D26f1eAbA1e32b (1000 ETH)
(3) 0xE11BA2b4D45Eaed5996Cd0823791E0C93114882d (1000 ETH)
(4) 0xd03ea8624C8C5987235048901fB614fDcA89b117 (1000 ETH)
(5) 0x95cED938F7991cd0dFcb48F0a06a40FA1aF46EBC (1000 ETH)
(6) 0x3E5e9111Ae8eB78Fe1CC3bb8915d5D461F3Ef9A9 (1000 ETH)
(7) 0x28a8746e75304c0780E011BEd21C72cD78cd535E (1000 ETH)
(8) 0xACa94ef8bD5ffEE41947b4585a84BdA5a3d3DA6E (1000 ETH)
(9) 0x1dF62f291b2E969fB0849d99D9Ce41e2F137006e (1000 ETH)

Chain ID: 1337
RPC: http://127.0.0.1:8545
Port: 8545
```

---

## 8. GitHub Repository

```
https://github.com/cjayy/blockchain-lab
```

> All `.sol` source files and scripts are available in the repository. Clone with:
> ```bash
> git clone https://github.com/cjayy/blockchain-lab.git
> cd blockchain-lab
> npm install
> ```

---

## Notes for Scribe

- The `undefined` responses in the console are **normal** — JavaScript returns `undefined` for statements that don't produce a value
- The large `ContractTransactionResponse` object confirms the `setValue(42)` transaction was **successfully mined into block 2**
- The `revert Not authorised` error on the final line is **intentional and expected** — it is a required deliverable proving access control works
- Contract address will be **different on Day 2** after redeployment — this is normal blockchain behaviour
