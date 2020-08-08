# Chainlink on ELASTOS Sidechain
This guide provides some tips and links to resources for you to launch you Chainlink oracle node on the ELASTOS Sidechain. Most Chainlink specific documentation can already be found at https://docs.chain.link/ but this guide will cover some ELASTOS-specific requirements.

The 4 components to running Chainlink on the ELASTOS Sidechain are:
- Chainlink Node
- ELASTOS Sidechain Node
- API Adapters
- On-chain contracts & Job configuration

## Chainlink Node
The Chainlink node listens for on-chain requests for data and responds with the requested data in exchange for payment in LINK. To run a node you can follow the official documentation at https://docs.chain.link/docs/running-a-chainlink-node. Make sure to understand the requirements. You will need access to a PostgreSQL server and ELASTOS sidechain node.

If you are using Kubernetes, consider using our Helm chart https://github.com/vulcanlink/charts/tree/master/chainlink.

Because you will be runnign the node on ELASTOS, you will have to customize the `ETH_URL` and `LINK_CONTRACT_ADDRESS` environment variables. 

|                   Variable                   |                                                                                Description                                                                                |
|-----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
ETH_URL | Ethereum node connection url|
LINK_CONTRACT_ADDRESS | LINK token contract address

#### ETH_URL
Read below if you need info on running your Ethereum Sidechain.

If you are running the ELASTOS Helm chart in your K8s cluster you can use `ws://elastos-geth.default.svc.cluster.local:20635`. For the testing purposes, you can also use our public endpoint `ws://testelaeth.vulcan.link/ws`.

#### LINK_CONTRACT_ADDRESS
Currently there is no mainnet LINK token contract.

For ELASTOS testnet, Vulcan Link has deployed a contract at `0xec5e3d6f656f5d98ba919cf842327a3f1652fcf2`. Join our [Discord](https://discord.gg/uGwqJJH) and send your address in the `#faucet` channel if you need some test elaLINK.

## ELASTOS Sidechain Node
### Public Endpoints
The following ELASTOS Sidechain endpoints are provided by Vulcan Link. The Chainlink node requires a <b>Websocket</b> connection.
As a Chainlink node operator you should aim to <b>migrate to a self-hosted node as soon as possible</b>. These nodes are provided for convenience <b>ONLY</b> and should <b>NOT</b> be relied on for mission criticial operations.

|                   Network                   |                                                                                Protocol                                                                                |                            URL                            |
|-----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
Testnet | Websocket | ws://testelaeth.vulcan.link/ws
Testnet | HTTP | http://testelaeth.vulcan.link/
Mainnet | Websocket | ws://elaeth.vulcan.link/ws
Mainnet | HTTP | http://elaeth.vulcan.link/

### Self-hosted
While you may use the public Vulcan Link ELASTOS endpoint for the purpose of the PoC Tesnet, any future mainnet launch will require you to run your own ELASTOS Sidechain node to ensure reliability and censorship-resistance.
Listed below are 3 options on how to run you ELASTOS Sidechain node. If you are using Kubernetes with Helm, we recommend you look at the 3rd option of using our Helm repository.
#### Build from source
Build ELASTOS geth from source at https://github.com/elastos/Elastos.ELA.SideChain.ETH.
This is the most secure way to run your ELASTOS node.
```
make geth
geth
```
#### Docker Container
Vulcan Link provides a pre-built Docker image that you can pull and run:
```
docker run vulcanlink/elastos-geth
```
You can also use the source repository to build your own Docker image.
```
cd Elastos.ELA.SideChain.ETH
docker build . -t my-elastos-geth
```
#### Helm Chart for Kubernetes
If you are using K8s, you can use Vulcan Link's elastos-geth Helm chart. Helm charts store configuration info on how to coordinate resources on your K8s node.

Make sure to configure the `values.yml` file to fit your needs. The default configuration will launch a mainnet node and provision a `100GiB` persistent storage volume. The chart uses a StatefulSet to ensure that chain data is conserved and recovered automatically from persistent storage in the case of Pod failure.

See https://github.com/vulcanlink/charts/tree/master/elastos-geth for more info on how to deploy the chart.
If you're unfamiliar with Helm, checkout https://helm.sh/.
```
helm repo add vulcanlink https://vulcanlink.github.io/charts/
helm install my-release vulcanlink/elastos-geth
```

<b>NOTE:</b> If you do not wish to rely on Vulcan Link's docker image, you can still use the helm chart by overriding the `image.repository` value in the `values.yml` file you pass.



## API Adapters
The Chainlink node requires API adapters to connect to external APIs with a standardized interface. Add the relevant crypto API adapters to your node to fetch the price feeds you need.

See https://github.com/smartcontractkit/external-adapters-js for info on deploying the most common API adapters.
#### Adding an Adapter Bridge
Once your adapter endpoint is deployed, you can add API Adapters Bridges to your Chainlink node under the "Bridges" tab. See https://docs.chain.link/docs/node-operators for more info on External Adapters.
### Public Endpoints
#### Disclaimer
The following API adapter endpoints are provided by Vulcan Link <b>FOR TEST PURPOSES ONLY!!!</b>
Once you have your Chainlink node setup you should make sure to launch your own API adapters with your own API keys.
<b>WARNING: </b> Vulcan Link Public API adapters <b>MAY BE DEPRECATED AT ANY TIME</b>

|                   Name                   |                                                                                URL                                                                                |
|-----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
coinapi | https://dev.api.vulcan.link/api/v1/web/guest/default/ea-dev-coinapi
coingecko | https://dev.api.vulcan.link/api/v1/web/guest/default/ea-dev-coingecko
coinmarketcap | https://dev.api.vulcan.link/api/v1/web/guest/default/ea-dev-coinmarketcap
coinpaprika	| https://dev.api.vulcan.link/api/v1/web/guest/default/ea-dev-coinpaprika
cryptocompare | https://dev.api.vulcan.link/api/v1/web/guest/default/ea-dev-cryptocompare

## Contracts and Job configuration
- Setup Metamask with ELASTOS Sidechain
- Deploy Oracle and set Authorization
- Fund Node and create jobs

### Metamask
Go to https://elaeth.io/ to Download the ELASTOS Sidechain compatible Metamask extension. Note that you might have to temporarily disable regular Metamask in your browser when using ELAMetamask to avoid having 2 injected web3 instances. Go to "Add Network > Custom Network" and enter the ELASTOS Sidechain Network parameters.

### Oracle
Your will need some ELA tokens to fund your node and deploy contracts. You can go to https://faucet.elaeth.io/ to request some test ELA tokens. You can also join our [Discord](https://discord.gg/uGwqJJH) and send your address in the #faucet channel if you need some test ELA.

Follow the instructions from https://docs.chain.link/docs/fulfilling-requests#deploy-your-own-oracle-contract to deploy your Oracle contract. If deploying on Testnet ELASTOS Sidechain, use `0xec5e3d6f656f5d98ba919cf842327a3f1652fcf2` as the LINK token address. Currently, there is no LINK token on the Mainnet.

Once the contract is deployed. Authorize your node to fulfill requests from the Oracle https://docs.chain.link/docs/fulfilling-requests#add-your-node-to-the-oracle-contract.

### Fund node and create jobs
You can find your node's address under the "Configuration" tab on the Chainlink Dashboard. Fund your node with some ELA such that it may respond to Chainlink Requests.

Create ELA/USD jobs for each API adapter you have deployed. Make sure to update the `ORACLE_ADDRESS` and `ADAPTER_NAME` parameters.
```

{
  "initiators": [
    {
      "type": "runlog",
      "params": {
        "address": "ORACLE_ADDRESS"
      }
    }
  ],
  "tasks": [
    {
      "type": "ADAPTER_NAME",
      "confirmations": null,
      "params": {
        "to": "USD",
        "from": "ELA"
      }
    },
    {
      "type": "copy",
      "confirmations": null,
      "params": {
        "copyPath": [
          "result"
        ]
      }
    },
    {
      "type": "multiply",
      "confirmations": null,
      "params": {
        "times": 100000000
      }
    },
    {
      "type": "ethint256",
      "confirmations": null,
      "params": {
      }
    },
    {
      "type": "ethtx",
      "confirmations": null,
      "params": {
      }
    }
  ],
  "startAt": null,
  "endAt": null
}
```

## Done !
Your node is setup and ready to respond to requests. If you wish to test you node, you can follow additional instructions from the Chainlink Docs to deploy your own Consumer contract https://docs.chain.link/docs/fulfilling-requests#create-a-request-to-your-node.

Join our [Discord](https://discord.gg/uGwqJJH) for questions, support and to get added to Testnet Aggregators.