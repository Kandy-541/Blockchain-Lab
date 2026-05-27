QA testser tasks - day 1

Verify command output matches expected results

codes: 

to enter in the hardhat console:

1.
```
 npx hardhat console --network localhost

```

Then paste one line at a time, waiting for the > prompt to return before typing the next:
2. 
```
const Storage = await ethers.getContractFactory("SimpleStorage");

```
3. 
```
const contract = await Storage.attach("0xe78A0F7E598Cc8b0Bb87894B0F60dD2a88d6a8Ab");

```

Note: "0xe78A0..." is the contract address, check documentation for more info

5.
```
 await contract.setValue(42);

```
6. 
```
const val = await contract.getValue();

```
7. 
```console.log(val.toString());

```

Note: Should print 42. Then test the access control:

8. 
```
const [owner, notOwner] = await ethers.getSigners();

```
9. 
```await contract.connect(notOwner).setValue(99);

```

Note: That last one should throw an error containing "Not authorised", check the documentation to see the whole console output, or day1 md report
Then exit cleanly:
10. .exit

To see everything clearly, check the day1 report, open it in GitHub or somewhere that supports md files, the codes and the text will be separated cleanly explaining everything, or wait for Marlyse report.