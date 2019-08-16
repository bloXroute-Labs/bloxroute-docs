## Installation of the bloXroute Gateway 

This document describes installation of the bloXroute Gateway for the bloXroute Ethereum Miner Test. For this test, it is expected that you are running an Ethereum geth node connected to the mainnet. 

The bloXroute Gateway is an application that connects your blockchain full node to the bloXroute block distribution network. Using the Gateway, the block distribution network is designed to propagate blocks to your full node faster than the traditional peer-to-peer blockchain network. It is recommended that you install the Gateway on a separate server from your full node, on a server with low latency to your full node.

This document describes two ways that you may install the bloXroute Gateway and the Blockchain-Logger, using either packaged docker images, or via a github download. In future versions of the Gateway we expect to provide pre-packaged 'pip install' options. 


### Installing the bloXroute Gateway from Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This permits an easy, quick installation. 

This section assumes that you have Docker installed. If you would like to install Docker to use the bloXroute gateway, please visit: https://docs.docker.com/install/  There you can find installation notes for the common versions of Linux, MacOS, etc. 

Currently, we are only distributing our docker image from a private Docker Hub repository. (In the future, we expect to make our docker image public.) Accordingly, to access the docker image you will need a Docker Hub account.

1. Please ensure that the needed ports are open on your firewall. The bloXroute Gateway requires port 9001 be open. 

1. Create username/password on Docker Hub (https://hub.docker.com/).

2. Provide bloXroute with username or email address for the account. We will add you to our team so you can access the docker image. 

1. Determine your node ID for your ethereum full node. You can retrieve your node ID using the ethereum rpc command [admin.nodeInfo](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#admin_nodeinfo). The hexadecimal node ID is encoded in the username portion of the enode URL, separated from the host by an @ sign (For more, see https://github.com/ethereum/wiki/wiki/enode-url-format).

3. Docker login

    ```
    docker login --username [username]
    ```
	
4. Pull gateway image

   ```
   docker pull bloxroute/bxgateway:latest
   ```
	
5. Run the docker as a daemon. The gateway accepts various parameters as described in the Gateway Parameters section below. The parameters may be passed in the run command to provide the IP address and port of the full node, etc. If using the "external-port" parameter, please update "-p 9001:9001" to "-p (new port):(new port)". Be sure to replace "\<YOUR NODE ID\>" with your node id. 
   ```
   docker run -d --name gateway -p 9001:9001 bloxroute/bxgateway:latest --blockchain-protocol Ethereum --blockchain-network Mainnet --blockchain-ip 172.17.0.1 --blockchain-port 30303  --node-public-key="<YOUR NODE ID>"
   ```


### Pip Installation

This section describes the installation of the bloXroute Gateway using pip. These instructions assume you are running Ubuntu 18.04, Centos, or Alpine Linux with Python 3.6 or 3.7 installed. Additional steps may be necessary depending on your system. Please see [https://pypi.org/project/bloxroute-gateway/#description](https://pypi.org/project/bloxroute-gateway/#description) for more. 

1. Determine your node ID for your ethereum full node. You can retrieve your node ID using the ethereum rpc command [admin.nodeInfo](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#admin_nodeinfo). The hexadecimal node ID is encoded in the username portion of the enode URL, separated from the host by an @ sign (For more, see https://github.com/ethereum/wiki/wiki/enode-url-format).

1. Install via pip 
   ```
    pip install bloxroute-gateway
    ```
2. Run the gateway. The gateway accepts various parameters as described in the Gateway Parameters section below. The parameters may be passed to provide the IP address and port of the full node, etc.  Be sure to replace "\<YOUR NODE ID\>" with your node id. 
   ```
   bloxroute_gateway --blockchain-protocol Ethereum --blockchain-network Mainnet  --node-public-key="<YOUR NODE ID>"
   ```

### Gateway Parameters
**Required Gateway Parameters**

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| blockchain-protocol | Blockchain protocol gateway is connecting to. This test is only on the Ethereum network. | Ethereum |
| blockchain-network | Blockchain network gateway is connecting to (Mainnet or Testnet). This test is only on the Mainnet. | Mainnet |
| node-public-key | node ID of your ethereum node. You can retrieve your node ID using the ethereum rpc command [admin.nodeInfo](https://github.com/ethereum/go-ethereum/wiki/Management-APIs#admin_nodeinfo).  | N/A |

**Optional Gateway Parameters**

These parameters should not be changed unless needed. 

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| external-ip | External IP is the external network IP of the Gateway on which the gateway can receive external connections from other gateways in the network. | None |
| external-port | External port is the port on which the gateway can receive external connections from other gateways in the network. The default is 9001. | 9001 |
| blockchain-ip | The IP address of your Ethereum full node. Default is "127.0.0.1".  | "127.0.0.1" |
| blockchain-port | The port of your Ethereum full node. Default is 30303.   | 30303 |
| continent | The continent of your Ethereum full node. Valid `continent` values are `NA` (North America), `SA` (South and Central America), `EU` (Europe), `OC` (Oceania), `AS` (Asia), `AF` (Africa), `AN` (Antarctica). | AS |



