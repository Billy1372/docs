# NuLink Worker Initialization and Running  

If you install your Worker node via Docker, you can initialize and run it following this [instruction](https://docs.nulink.org/products/nulink_worker/worker_running#Run-Node-via-Docker). And if you install your Worker node via local installation, please read [local operation](https://docs.nulink.org/products/nulink_worker/worker_running#Run-Node-via-Local-Installtion) 


## Run Node via Docker (Recommended)  

There are there steps to complete starting a Worker node via Docker:
1. **Export Node Environment Variables**
2. **Initialize Node Configuration**
3. **Launch the Node**

### Export Node Environment Variables  
These environment variables are used to better simplify the Docker installation process.
```shell
# Password used for lock/unlock the private storage generated by NuLink Worker.You can choose some characters(8 minimum) as the password here. Do not forget it!!!
$ export NULINK_KEYSTORE_PASSWORD=<YOUR NULINK STOREAGE PASSWORD>

# Password used to unlock the keystore file of Worker account, you should have set it up when you create the Worker account using Geth or other sources. Make sure you enter the same one!!!
$ export NULINK_OPERATOR_ETH_PASSWORD=<YOUR WORKER ACCOUNT PASSWORD>
```

### Initialize Node Configuration  
This step creates and stores the NuLink worker node configuration, and only needs to be run once.

```shell
$ docker run -it --restart on-failure \
-p 9151:9151 \
-v </path/to/host/machine/directory>:/code \
-v </path/to/host/machine/directory>:/home/circleci/.local/share/nulink \
-e NULINK_KEYSTORE_PASSWORD \
nulink/nulink nulink ursula init \
--signer <ETH KEYSTORE URI> \
--eth-provider <NULINK PROVIDER URI>  \
--network <NULINK NETWORK NAME> \
--payment-provider <PAYMENT PROVIDER URI> \
--payment-network <PAYMENT NETWORK NAME> \
--operator-address <WORKER ADDRESS> \
--max-gas-price <GWEI>
```
Replace the following values with your own value:
- `</path/to/host/machine/directory>` - The host directory you create when install.
- `<ETH KEYSTORE URI>` - The path to store the keystore of the Worker account in container.
- `<NULINK PROVIDER URI>` - The URI of a local or hosted node where NuLink testnet launched.
- `<NULINK NETWORK NAME>` - The name of the network where NuLink testnet launched. 
- `<PAYMENT PROVIDER URI>` - The URI of a local or hosted node where payment goes.
- `<PAYMENT NETWORK NAME>` - The name of the payment network. 
- `<OPERATOR ADDRESS>` - The address of the Worker account. [How to generate Worker account](./eth_account.md).
- `<GWEI>` (Optional) - The maximum price of gas to spend on any transaction.

Example Input:

```shell
$ docker run -it --restart on-failure \
-p 9151:9151 \
-v /nulink:/code \
-v /nulink:/home/circleci/.local/share/nulink \
-e NULINK_KEYSTORE_PASSWORD \
nulink/nulink nulink ursula init \
--signer keystore:///code/keystore_directory/keystore_file \
--eth-provider https://data-seed-prebsc-2-s2.binance.org:8545 \
--network bsc_testnet \
--payment-provider https://data-seed-prebsc-2-s2.binance.org:8545 \
--payment-network bsc_testnet \
--operator-address 0x7DEff413E415bd2507da4988393d8540a28bf3c6 \
--max-gas-price 100
```
Example Output:

```shell
# step 1
 Detected IPv4 address (8.219.188.70) - Is this the public-facing address of Worker? [y/N]: y
 
 Please provide a password to lock Operator keys.
 Do not forget this password, and ideally store it using a password manager.
 
 # step 2
 Enter nulink keystore password (8 character minimum): xxxxxx
 Repeat for confirmation: xxxxxx
 
 Backup your seed words, you will not be able to view them again.
 
 hammer fatal jazz era hurt shoulder stand story find move earn  much actor animal stamp know vital odor coin electric torch quick siege tonight
 
 # step 3
 Have you backed up your seed phrase? [y/N]: y
 
 # step 4
 Confirm seed words: hammer fatal jazz era hurt shoulder stand story find move earn  much actor animal stamp know vital odor coin electric torch quick siege tonight
 
 
 Generated keystore
 
 
 Public Key:   02dacea4c7f5563004af37f282ca10f7
 Path to Keystore: /root/.local/share/nulink/keystore
 
 - You can share your public key with anyone. Others need it to interact with you.
 - Never share secret keys with anyone!
 - Backup your keystore! Character keys are required to interact with the protocol!
 - Remember your password! Without the password, it's impossible to decrypt the key!
 
 
 Generated configuration file at non-default filepath /root/.local/share/nulink/ursula-02dacea4.json
 * NOTE: for a non-default configuration filepath use `--config-file "/root/.local/share/nulink/ursula-02dacea4.json"` with subsequent `ursula` CLI commands
 
 * Review configuration  -> nulink ursula config
 * Start working         -> nulink ursula run
```

### Launch the Node  
The following command will start the node. Make sure you use the same host directory as the configuration. 

```shell
$ docker run --restart on-failure -d \
--name ursula \
-p 9151:9151 \
-v </path/to/host/machine/directory>:/code \
-v </path/to/host/machine/directory>:/home/circleci/.local/share/nulink \
-e NULINK_KEYSTORE_PASSWORD \
-e NULINK_OPERATOR_ETH_PASSWORD \
nulink/nulink nulink ursula run --no-block-until-ready
```

Example Input：
```shell
$ docker run --restart on-failure -d \
--name ursula \
-p 9151:9151 \
-v /nulink:/code \
-v /nulink:/home/circleci/.local/share/nulink \
-e NULINK_KEYSTORE_PASSWORD \
-e NULINK_OPERATOR_ETH_PASSWORD \
nulink/nulink nulink ursula run --no-block-until-ready
```
Example Output:

```shell
8d269b8e845ae0ba7bb9ad106030da1789b0da73554a72fa4b779f1bf4437bd1
```

### View Worker Static IP Address
The following command describes how to view worker addresses.

```shell
    docker logs -f ursula3
```
e.g.

Example Input:
```shell
    docker logs -f ursula3
```
Example Output:
```shell
    Authenticating Ursula
    Loaded Ursula (bsc_testnet)
    ✓ External IP matches configuration
    Starting services
    ✓ Node Discovery (Bsc_testnet)
    ✓ Work Tracking
    ✓ Start Operator Bonded Tracker
    ✓ Rest Server https://8.219.188.70:9151
    Working ~ Keep Ursula Online!
```

Now you can save the Node URI(e.g: https://8.219.188.70:9151) and Worker address(e.g: 0x7DEff413E415bd2507da4988393d8540a28bf3c6)  for bonding operation.

## Run Node via Local Operation  

There are two steps to complete starting a Worker node via local operation:
1. **Initialize Node Configuration**
2. **Launch the Node**

### Initialize Node Configuration

This step creates and stores the worker node configuration, and only needs to be run once.

```shell
nulink ursula init      \
--signer <ETH KEYSTORE URI>           \
--network <NULINK NETWORK NAME>           \
--eth-provider <NULINK PROVIDER URI>      \
--payment-provider <PAYMENT PROVIDER URI>  \
--payment-network <PAYMENT NETWORK NAME>   \
--operator-address <OPERATOR ADDRESS> \
--max-gas-price <GWEI>
```


- `<ETH KEYSTORE URI>` - The path to store the keystore of the Worker account.
- `<NULINK PROVIDER URI>` - The URI of a local or hosted node where NuLink testnet launched.
- `<NULINK NETWORK NAME>` - The name of the network where NuLink testnet launched. 
- `<PAYMENT PROVIDER URI>` - The URI of a local or hosted node where payment goes.
- `<PAYMENT NETWORK NAME>` - The name of the payment network. 
- `<OPERATOR ADDRESS>` - The address of the Worker account. [How to generate Worker account](./eth_account.md)
- `<GWEI>` (Optional) - The maximum price of gas to spend on any transaction.

Example Input:

```shell
nulink ursula init \
--signer keystore:///root/nulink/keystore \
--eth-provider https://data-seed-prebsc-2-s2.binance.org:8545 \
--network bsc_testnet \
--payment-provider https://data-seed-prebsc-2-s2.binance.org:8545 \
--payment-network bsc_testnet \
--operator-address 0x8a20d379A4C08c482a617A81a39EB426B6EB8642 \
--max-gas-price 2000
```

Example Output:

```shell
# step 1
Detected IPv4 address (8.219.188.70) - Is this the public-facing address of Worker? [y/N]: y

Please provide a password to lock Operator keys.
Do not forget this password, and ideally store it using a password manager.

# step 2
Enter nulink keystore password (8 character minimum): xxxxxx
Repeat for confirmation: xxxxxx

Backup your seed words, you will not be able to view them again.

hammer fatal jazz era hurt shoulder stand story find move earn  much actor animal stamp know vital odor coin electric torch quick siege tonight

# step 3
Have you backed up your seed phrase? [y/N]: y

# step 4
Confirm seed words: hammer fatal jazz era hurt shoulder stand story find move earn  much actor animal stamp know vital odor coin electric torch quick siege tonight


Generated keystore


Public Key:   02dacea4c7f5563004af37f282ca10f7
Path to Keystore: /root/.local/share/nulink/keystore

- You can share your public key with anyone. Others need it to interact with you.
- Never share secret keys with anyone! 
- Backup your keystore! Character keys are required to interact with the protocol!
- Remember your password! Without the password, it's impossible to decrypt the key!


Generated configuration file at non-default filepath /root/.local/share/nulink/ursula-02dacea4.json
* NOTE: for a non-default configuration filepath use `--config-file "/root/.local/share/nulink/ursula-02dacea4.json"` with subsequent `ursula` CLI commands

* Review configuration  -> nulink ursula config
* Start working         -> nulink ursula run
```

### Launch the Node

Run worker node using the initialized configuration.

```shell
nulink ursula run --no-block-until-ready
```

Enter the above startup command and press enter to prompt for the passwords of ETH account and NuLink keystore, which are set in the initialization phase.

Note: **_operator account needs to have tokens on the corresponding chain_**


Example Input:

```shell
nulink ursula run --no-block-until-ready
```

Example Output:

```shell
Enter ethereum account password (0x7bD7B1266868B34dA4929501FfEA4ac737dA0E93):
Enter nulink keystore password:
Authenticating Ursula
Loaded Ursula (heco_testnet)
✓ External IP matches configuration
Starting services
✓ Node Discovery (Heco_testnet)
✓ Operator 0x7bD7B1266868B34dA4929501FfEA4ac737dA0E93 is funded with 0.499959405 ETH
✓ Operator 0x7bD7B1266868B34dA4929501FfEA4ac737dA0E93 is bonded to staking provider 0xf3D6ad89E34b1Cf8325EA614fa901eA4F34Be14a
✓ Operator already confirmed.  Not starting worktracker.
✓ Start Operator Bonded Tracker
✓ Rest Server https://8.219.188.70:9157
Working ~ Keep Ursula Online!
```

Now you can save the Worker address(e.g: 0xf3D6ad89E34b1Cf8325EA614fa901eA4F34Be14a) and Node URI(e.g: https://8.219.188.70:9157) for bonding operation.



Remark: If you use a LAN address in the first step, please use --no-ip-checkup. If you start the node without binding, use --no-block-until-ready
