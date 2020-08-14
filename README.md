# Chainlink on ELASTOS Sidechain
This guide provides some tips and links to resources for you to launch you Chainlink oracle node on the ELASTOS Sidechain. Most Chainlink specific documentation can already be found at https://docs.chain.link/ but this guide will cover some ELASTOS-specific requirements.

The 3 components to running Chainlink on the ELASTOS Sidechain are:
- Chainlink Node & ELASTOS Sidechain Node
- API Adapters
- On-chain contracts & Job configuration

## Chainlink Node
The Chainlink node listens for on-chain requests for data and responds with the requested data in exchange for payment in LINK. You will need access to a PostgreSQL server and ELASTOS sidechain node.

If you are using Kubernetes, consider using our Helm chart https://github.com/vulcanlink/charts/tree/master/chainlink.

You can also use the official documentation at https://docs.chain.link/docs/running-a-chainlink-node. 


Because you will be running the node on ELASTOS, you will have to customize the `ETH_URL` and `LINK_CONTRACT_ADDRESS` environment variables. 

| Variable | Description | ELASTOS Testnet Value | ELASTOS Mainnet Value |
|----------|-------------|-----------------------|-----------------------|
`ETH_URL` | Ethereum node connection url | `ws://testelaeth.vulcan.link/ws` | `TBD`
`LINK_CONTRACT_ADDRESS` | LINK token contract address | `0xec5e3d6f656f5d98ba919cf842327a3f1652fcf2` | `TBD`

#### ETH_URL
For testing purposes, you can use our public endpoint `ws://testelaeth.vulcan.link/ws`.
We recommend you checkout [ELASTOS_GETH](./ELASTOS_GETH.md) fore more info on running your own ELASTOS Sidechain node (required for Mainnet).


#### LINK_CONTRACT_ADDRESS
Currently there is no mainnet LINK token contract.

For ELASTOS testnet, Vulcan Link has deployed a contract at `0xec5e3d6f656f5d98ba919cf842327a3f1652fcf2`. Join our [Discord](https://discord.gg/uGwqJJH) and send your address in the `#faucet` channel if you need some test elaLINK.

## API Adapters
The Chainlink node requires API adapters to connect to external APIs with a standardized interface. Add the relevant crypto API adapters to your node to fetch the price feeds you need.

See https://github.com/smartcontractkit/external-adapters-js for info on deploying the most common API adapters. Also see [EXTERNAL_ADAPTERS](./EXTERNAL_ADAPTERS.md) for some public pre-deployed adapter endpoints.

## Contracts and Job configuration
- Setup Metamask with ELASTOS Sidechain
- Deploy Oracle and set Authorization
- Fund Node and create jobs

### Metamask
Go to https://elaeth.io/ to Download the ElaEth compatible Metamask extension for your browser (Chrome / Firefox / Opera). Go to the "Extensions" tab in your Chrome browser and turn on "Developer Mode". Now you can drag and drop the downloaded ELASTOS Metamask.

If you also have regular Metamask installed, you should temporarily disable  it to avoid having 2 injected web3 instances. 

Setup ElaEth Metamask with you password and recovery phrase. Go to "Add Network > Custom Network" and enter the ELASTOS Sidechain Network parameters:

| Network Name | RPC URL | Explorer URL | Symbol |
|----------|-------------|-----------------------|-----------------------|
 ELASTOS | `https://mainrpc.elaeth.io` | `https://explorer.elaeth.io` | `ELA`
 ELASTOS Testnet | `https://rpc.elaeth.io` | `http://testnet.elaeth.io` | `ELA`

### ELA faucet
Your will need some ELA tokens to fund your node and deploy contracts. You can go to https://faucet.elaeth.io/ to request some test ELA tokens. You can also join our [Discord](https://discord.gg/uGwqJJH) and send your address in the #faucet channel if you need some test ELA.

### Deploy Oracle
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