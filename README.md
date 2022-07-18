# Bring up firefly-fabconnect

This documentation shows several ways to deploy firefly-fabconnect:
- Using `fabconnect` and the [Fabric `test-network`](https://github.com/hyperledger/fabric-samples/tree/main/test-network) in the samples repository.
- Using `fabconnect` and the [Fabric `test-network-nano-bash`](https://github.com/hyperledger/fabric-samples/tree/main/test-network-nano-bash) in the samples repository. __in progress__
- Using `FireFly CLI`

  > **NOTE**: The `FireFly CLI` run some Supernodes on your machine. The stacks contains an instance of firefly-core, firefly-dataexchange-https, ipfs and the firefly-sandbox.

  👀 Si entiendes el español no dudes en visitar el [README-ES](./README-ES.md).
  
## Table of Contents

- [Using fabconnect and the Fabric test-network](#fabconnect_testnetwork)
  * [Download fabric-samples, docker images, and fabric binaries](#fabconnect_testnetwork_download_prerequisites)
  * [Bring up the test network](#fabconnect_testnetwork_bringup_testnetwork)
  * [Download configuration](#fabconnect_testnetwork_download_fabconnect)
  * [Bring up `fabconnect`](#fabconnect_testnetwork_bringup_fabconnect)
- [Using fabconnect and the Fabric test-network-nano-bash](#fabconnect_testnetworknanobash)
- [Using firefly-cli](#fireflycli)
  * [Download and install the __CLI__](#download_installation)
  * [Create a stack of __Hyperledger Fabric__](#stack_fabric)
  * [Configuration files](#config_files)
- [Interacting with the `asset-transfer-basic` chaincode](#fabconnect_testnetwork_interacting_cc)
  * [In `fabconnect` and `test-network` mode (including `test-network-nano-bash`)](#fabconnect_testnetwork_interacting_cc_mode1)
  * [In `firefly-cli` mode](#fabconnect_testnetwork_interacting_cc_mode2)
- [Documentation](#doc)

## Using fabconnect and the Fabric test-network<a name="fabconnect_testnetwork"></a>

### Download fabric-samples, docker images, and fabric binaries<a name="fabconnect_testnetwork_download_prerequisites"></a>

```bash
cd $HOME
```
To get the install script:

```bash
curl -sSLO https://raw.githubusercontent.com/hyperledger/fabric/main/scripts/install-fabric.sh && chmod +x install-fabric.sh
```

Run the script:
```bash
./install-fabric.sh -f 2.4.4 d b s
```
> **NOTE**: These arguments download the `Fabric Images` 2.4.4 version, clone the `fabric-samples` repository, and download the `Fabric binaries`.

[Visit this link for better instructions...](https://hyperledger-fabric.readthedocs.io/en/latest/install.html)

### Bring up `"test-network"`<a name="fabconnect_testnetwork_bringup_testnetwork"></a>

```bash
cd fabric-samples/test-network
```
Run the following command to start the network, create a channel with  `mychannel` default name, and generate the cryptographic artifacts with the Fabric CA:

```bash
./network.sh up createChannel -ca -c mychannel
```

### Download configuration<a name="fabconnect_testnetwork_download_fabconnect"></a>

Download this repository
```bash
git clone https://github.com/kmilodenisglez/fabconnect-testnet.git
```
> **NOTE**: This repository has a docker-compose file that starts a firefly-fabconnect container. It is configured to mount the cryptographic artifacts volume from `$HOME/fabric-samples/test-network/organizations/`.

```bash
cd fabconnect
```
### Bring up `fabconect`<a name="fabconnect_testnetwork_start_fabconnect"></a>

```bash
docker-compose up -d
```

Visit the url: http://__ip_address_here__:3000/api

## Using fabconnect and the Fabric test-network-nano-bash<a name="fabconnect_testnetworknanobas"></a>

In progress...

## Using firefly-cli<a name="fireflycli"></a>

### Download and installation the __CLI__<a name="download_installation"></a>
Follow the steps from the official README for [download](https://github.com/hyperledger/firefly-cli/#download-the-package-for-your-os) and [install](https://github.com/hyperledger/firefly-cli/#extract-the-binary-and-move-it-to-usrbinlocal) from the cli.

### Create a stack of __Hyperledger Fabric__<a name="stack_fabric"></a>
The `ff init` command creates a new stack and will prompt you for a few details, such as the members number (organizations), the organization(s) name and peer nodes name.
```bash
ff init -b fabric --prompt-names stack-fabric
```
Once the execution of the command has finished, you should see an output similar to this:

```
initializing new FireFly stack...
number of members: 1
name for org 0: org1
name for node 0: peer1
Stack 'stack-fabric' created!
To start your new stack run:

  ff start stack-fabric
```
> **NOTE**: In this example the stack `stack-fabric` was created with a single member named `"org1"` and a node named `"peer1"`.

### Configuration files<a name="config_files"></a>

> **NOTE**: The configuration files for each stack are located in `~/.firefly/stacks/`

## Interacting with the `asset-transfer-basic` chaincode<a name="fabconnect_testnetwork_interacting_cc"></a>

### In `fabconnect` and `test-network` mode (including `test-network-nano-bash`)<a name="fabconnect_testnetwork_interacting_cc_mode1"></a>

- If you operate with the `test-network` you can install the `asset-transfer-basic` chaincode with the steps: [Install the asset-transfer-basic on test-network](https://hyperledger-fabric.readthedocs.io/en/latest/test_network.html#starting-a-chaincode-on-the-channel)

- If you operate with `test-network-nano-bash` you can install the `asset-transfer-basic` chaincode with the steps: [Install the asset-transfer-basic on test-network-nano-bash](https://github.com/hyperledger/fabric-samples/tree/main/test-network-nano-bash#instructions-for-deploying-and-running-the-basic-asset-transfer-sample-chaincode)

> **NOTE**: To interact with the fabconnect endpoints you can access the link http://__ip_address__:3000/api in your browser.

### InitLedger
Invoke the POST /transactions endpoint, with the following data:
```json
{
  "headers": {
    "type": "SendTransaction",
    "signer": "admin",
    "channel": "mychannel",
    "chaincode": "basic"
  },
  "func": "InitLedger",
  "args": [],
  "init": false
}
```
As a result, the client should respond with a JSON similar to this:
```json
{
  "headers": {
    "id": "cee5a0b8-e207-49c9-76a2-ca0d106fa139",
    "type": "TransactionSuccess",
    "timeReceived": "2022-07-18T06:00:37.323214822Z",
    "timeElapsed": 2.217562622,
    "requestOffset": "",
    "requestId": ""
  },
  "blockNumber": 6,
  "signerMSP": "Org1MSP",
  "signer": "admin",
  "transactionID": "44574737473df89f0183827f10ede4b5e99563ba44df6b7d48a49763d9179228",
  "status": "VALID"
}
```

### QueryAllAssets
Invokes the POST /query endpoint, with the following data:
```json
{
  "headers": {
    "signer": "admin",
    "channel": "mychannel",
    "chaincode": "basic"
  },
  "func": "GetAllAssets",
  "args": [
    "string"
  ],
  "strongread": true
}
```
As a result, the client should respond with a JSON similar to this:
```json
{
  "headers": {
    "channel": "mychannel",
    "timeReceived": "",
    "timeElapsed": 0,
    "requestOffset": "",
    "requestId": ""
  },
  "result": [
    {
      "AppraisedValue": 300,
      "Color": "blue",
      "ID": "asset1",
      "Owner": "Tomoko",
      "Size": 5
    },
    {
      "AppraisedValue": 400,
      "Color": "red",
      "ID": "asset2",
      "Owner": "Brad",
      "Size": 5
    },
    ...
  ]
}
 ```
### In `firefly-cli` mode <a name="fabconnect_testnetwork_interacting_cc_mode2"></a>
If you use `firefly-cli` you can install and interact with the `asset-transfer-basic` chaincode by following the steps: [Working asset-transfer-basic with firefly-cli](https://hyperledger.github.io/firefly/tutorials/custom_contracts/fabric.html#work-with-hyperledger-fabric-chaincodes)

## Documentation <a name="doc"></a>

- [FireFly Fabconnect - Github](https://github.com/hyperledger/firefly-fabconnect)
- [FireFly Cli - Github](https://github.com/hyperledger/firefly-cli/)
- [Firefly - Doc](https://hyperledger.github.io/firefly/)