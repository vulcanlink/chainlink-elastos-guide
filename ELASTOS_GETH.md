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