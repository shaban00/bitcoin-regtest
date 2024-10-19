# Bitcoin Testing Using Docker

## Prerequisites

- **Docker**: Ensure you have Docker installed on your machine.

## Running the Bitcoin Docker Image

To start a Bitcoin node using Docker, execute the following command:

```bash
docker run --rm -d \
  --name bitcoin-node \
  -p 18443:18443 \
  -p 18444:18444 \
  -v bitcoin-data:/root/.bitcoin \
  ruimarinho/bitcoin-core \
  -printtoconsole \
  -regtest=1 \
  -rpcuser=admin \
  -rpcpassword=admin \
  -rpcallowip=0.0.0.0/0 \
  -rpcbind=0.0.0.0 \
  -debug=rpc
```

### Check If the Container Is Running

To verify that the `bitcoin-node` container is running, use:

```bash
docker ps --filter "name=bitcoin-node"
```

### Fetch Blockchain Information

To retrieve blockchain information, execute the following command:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin getblockchaininfo
```

### Check Node Status

To check the status of the node, run:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin getconnectioncount
```

### Simulate Transactions Between Alice and Bob

#### Create Wallet for Alice

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin createwallet "alice"
```

### Output

```bash
{
  "name": "alice",
  "warning": ""
}
```

#### Create Wallet for Bob

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin createwallet "bob"
```

### Output

```bash
{
  "name": "bob",
  "warning": ""
}
```

### List Addresses for Alice's Wallet

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice listreceivedbyaddress 1 true
```

### Output

```bash
[
]
```

### List Addresses for Bob's Wallet

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob listreceivedbyaddress 1 true
```

### Output

```bash
[
]
```

### Create Address for Alice's Wallet

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice getnewaddress
```

### Output

```bash
bcrt1qgj2lgrn7mjwntdu567e36yulftmjtet2d3xdhe
```

### Create Address for Bob's Wallet

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob getnewaddress
```

### Output

```bash
bcrt1qtleeq08pvkgws3czktdjn56l3dxrzekx2t9fux
```

### Check Addresses for Both Wallets

To check Alice's wallet:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice listreceivedbyaddress 1 true
```

### Output

```bash
[
  {
    "address": "bcrt1qgj2lgrn7mjwntdu567e36yulftmjtet2d3xdhe",
    "amount": 0.00000000,
    "confirmations": 0,
    "label": "",
    "txids": [
    ]
  }
]
```

To check Bob's wallet:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob listreceivedbyaddress 1 true
```

### Output

```bash
[
  {
    "address": "bcrt1qtleeq08pvkgws3czktdjn56l3dxrzekx2t9fux",
    "amount": 0.00000000,
    "confirmations": 0,
    "label": "",
    "txids": [
    ]
  }
]
```

### Receive a Block Reward

To send 50 bitcoins to Alice's wallet, run:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin generatetoaddress 101 bcrt1qgj2lgrn7mjwntdu567e36yulftmjtet2d3xdhe
```

### Output

```
[
  "117ac6f38b0dbee57f774f7d86ba1f856eefb6e4961d9462433d53f93930c3d3",
  "0b7b7c9dd48f80f8676ea2934fb3f94afaf05815fd129bba114f6b8b64cfd870",
  ...,
  "1b56ab069933d4d68bae095ba47440ed49137fc42289ae3a6c8bc054587b35ad",
  "67435c7fe26224136a98c461fb43f92de6db319758aa61b323037c46d64760d8"
]

```

### Verify Alice's Balance

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice getbalance
```

### Output

```bash
50.00000000
```

### Verify Bob's Balance

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob getbalance
```

### Output

```bash
0.00000000
```

### Sending Coins from Alice to Bob

To send 15 bitcoins from Alice to Bob, use:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice -named sendtoaddress address=bcrt1qtleeq08pvkgws3czktdjn56l3dxrzekx2t9fux amount=15 fee_rate=1000
```

### Output

```bash
df1b20a941207419523db7cd0c616ad93ec20316d36eb548e94b14f07d479dbb
```

### Verify Transaction Details

To check the transaction details, run:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice gettransaction df1b20a941207419523db7cd0c616ad93ec20316d36eb548e94b14f07d479dbb
```

### Output

```bash
{
  "amount": -15.00000000,
  "fee": -0.00141000,
  "confirmations": 0,
  "trusted": true,
  "txid": "df1b20a941207419523db7cd0c616ad93ec20316d36eb548e94b14f07d479dbb",
  "wtxid": "6d61c8304f5a4d5e565b9056d4d7f0ed860feb8f70a3023addd781c6958668c2",
  "walletconflicts": [
  ],
  "time": 1729296351,
  "timereceived": 1729296351,
  "bip125-replaceable": "yes",
  "details": [
    {
      "address": "bcrt1qtleeq08pvkgws3czktdjn56l3dxrzekx2t9fux",
      "category": "send",
      "amount": -15.00000000,
      "vout": 0,
      "fee": -0.00141000,
      "abandoned": false
    }
  ],
  "hex": "02000000000101a3b2055db41c185435c45d35c231f11b6575e20b2bc5c2b828c27a16e6cc06730000000000fdffffff02002f6859000000001600145ff3903ce16590e84702b2db29d35f8b4c3166c6389c9bd00000000016001459457382fdf31359d8c5cd7671e54829ac26c2780247304402204a68d5b526f48cbab1f4b5e1d32cea77e5f85e0cb3d1f697bf780618d2ed10190220387bc528b6145324ef0e410aafee377d623e6b669280c610ef1ce1e936667ddb012103a314a8f13cde6933e6ef9fe7e7f76848dceefc6dcbad8055b2597435f169aadb65000000"
}
```

### Check Alice's Confirmed and Unconfirmed Balance

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice getbalance
```

### Output

```bash
34.99859000
```

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice getunconfirmedbalance
```

### Output

```bash
0.00000000
```

### Check Bob's Confirmed and Unconfirmed Balance

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob getbalance
```

### Output

```bash
0.00000000
```

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob getunconfirmedbalance
```

### Output

```bash
15.00000000
```

**`NOTE`**: Note: Bob does not see the 15 BTC yet because the transaction requires at least one confirmation in RegTest.

### Mining Blocks to Confirm Transactions

To increase the confirmations for Alice's transaction, generate a new block:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice -generate 1
```

### Output

```bash
{
  "address": "bcrt1q04y5m6jsh3gxu9u5mgk6q7g3fhpec73wrkuv6s",
  "blocks": [
    "6ed2f8966f043260ec10483811035d1808feda8100a4271dce93ae1c5eceb350"
  ]
}
```

#### Recheck Transaction Details

After mining, confirm the transaction details again:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice gettransaction df1b20a941207419523db7cd0c616ad93ec20316d36eb548e94b14f07d479dbb
```

### Output

```bash
{
  "amount": -15.00000000,
  "fee": -0.00141000,
  "confirmations": 1,
  "blockhash": "6ed2f8966f043260ec10483811035d1808feda8100a4271dce93ae1c5eceb350",
  "blockheight": 102,
  "blockindex": 1,
  "blocktime": 1729296757,
  "txid": "df1b20a941207419523db7cd0c616ad93ec20316d36eb548e94b14f07d479dbb",
  "wtxid": "6d61c8304f5a4d5e565b9056d4d7f0ed860feb8f70a3023addd781c6958668c2",
  "walletconflicts": [
  ],
  "time": 1729296351,
  "timereceived": 1729296351,
  "bip125-replaceable": "no",
  "details": [
    {
      "address": "bcrt1qtleeq08pvkgws3czktdjn56l3dxrzekx2t9fux",
      "category": "send",
      "amount": -15.00000000,
      "vout": 0,
      "fee": -0.00141000,
      "abandoned": false
    }
  ],
  "hex": "02000000000101a3b2055db41c185435c45d35c231f11b6575e20b2bc5c2b828c27a16e6cc06730000000000fdffffff02002f6859000000001600145ff3903ce16590e84702b2db29d35f8b4c3166c6389c9bd00000000016001459457382fdf31359d8c5cd7671e54829ac26c2780247304402204a68d5b526f48cbab1f4b5e1d32cea77e5f85e0cb3d1f697bf780618d2ed10190220387bc528b6145324ef0e410aafee377d623e6b669280c610ef1ce1e936667ddb012103a314a8f13cde6933e6ef9fe7e7f76848dceefc6dcbad8055b2597435f169aadb65000000"
}
```

### Final Balance Check for Alice

To check Alice's final confirmed balance:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice getbalance
```

### Output

```bash
84.99859000
```

To check Alice's final unconfirmed balance:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=alice getunconfirmedbalance
```

### Output

```bash
0.00000000
```

### Final Balance Check for Bob

To check Bob's final confirmed balance:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob getbalance
```

### Output

```bash
15.00000000
```

To check Bob's final unconfirmed balance:

```bash
docker exec bitcoin-node bitcoin-cli -regtest -rpcuser=admin -rpcpassword=admin -rpcwallet=bob getunconfirmedbalance
```

### Output

```bash
0.00000000
```

### Cleanup: Remove the Docker Container

When you are finished, remove the Docker container with the following command:

```bash
docker rm -f bitcoin-node
```
