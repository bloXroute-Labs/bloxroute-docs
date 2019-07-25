## Installation of the bloXroute Gateway 

This document describes installation of the bloXroute Gateway for the bloXroute Ethereum Miner Test. For this test, it is expected that you are running an Ethereum geth node connected to the mainnet. 

The bloXroute Gateway is an application that connects your blockchain full node to the bloXroute block distribution network. Using the Gateway, the block distribution network is designed to propagate blocks to your full node faster than the traditional peer-to-peer blockchain network. It is recommended that you install the Gateway on the same server as your full node, or on a server with low latency to your full node. 

The Blockchain-Logger is a small piece of software created by bloXroute to record block propagation statistics across p2p blockchain networks. It does not provide any block propagation performance improvements and permits us to measure block propagation without the bloXroute network. 

This document describes two ways that you may install the bloXroute Gateway and the Blockchain-Logger, using either packaged docker images, or via a github download. In future versions of the Gateway we expect to provide pre-packaged 'pip install' options. 


## bloXroute Gateway

### Installing the bloXroute Gateway from Docker

This section describes the installation of the bloXroute Gateway in a pre-packaged Docker form. This permits an easy, quick installation. 

This section assumes that you have Docker installed. If you would like to install Docker to use the bloXroute gateway, please visit: https://docs.docker.com/install/  There you can find installation notes for the common versions of Linux, MacOS, etc. 

Currently, we are only distributing our docker image from a private Docker Hub repository. (In the future, we expect to make our docker image public.) Accordingly, to access the docker image you will need a Docker Hub account.

1. Please ensure that the needed ports are open on your firewall. The bloXroute Gateway requires port 9001 be open. 

1. Create username/password on Docker Hub (https://hub.docker.com/).

2. Provide bloXroute with username or email address for the account. We will add you to our team so you can access the docker image. 

3. Docker login

    ```
    docker login --username [username]
    ```
	
4. Pull gateway image

   ```
   docker pull bloxroute/bxgateway:latest
   ```
	
5. Run the docker as a daemon. The gateway accepts various parameters as described in the Gateway Parameters section below. The parameters may be passed in the run command to provide the IP address and port of the full node, etc. If using the "external-port" parameter, please update "-p 9001:9001" to "-p (new port):(new port)".
   ```
   docker run -d --name gateway -p 9001:9001 bloxroute/bxgateway:latest --blockchain-protocol Ethereum --blockchain-network Mainnet --blockchain-ip 172.17.0.1 --blockchain-port 30303 
   ```

    Once you've successfully installed the Gateway docker container, please stop the Gateway container and install the Blockchain-Logger. The Gateway will be used only during the second phase of our test and should be stopped during the first phase. You may stop the docker container by running:
    ```
    docker stop -t gateway
    ```

### Pip Installation

#### (Coming soon)

### Gateway Parameters
**Required Gateway Parameters**

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| blockchain-protocol | Blockchain protocol gateway is connecting to. This test is only on the Ethereum network. | Ethereum |
| blockchain-network | Blockchain network gateway is connecting to (Mainnet or Testnet). This test is only on the Mainnet. | Mainnet |

**Optional Gateway Parameters**

These parameters should not be changed unless needed. 

| Parameter | Description | Example |
| --------- | ----------- | -------------|
| external-port | External port is the port on which the gateway can receive external connections from other gateways in the network. The default is 9001. | 9001 |
| blockchain-ip | The IP address of your Bitcoi Cash full node. Default is "127.0.0.1".  | "127.0.0.1" |
| blockchain-port | The port of your Ethereum full node. Default is 8333. Because we are using the testnet, this should be set to 18333.  | 8333 |
| continent | The continent of your Ethereum full node. Valid `continent` values are `NA` (North America), `SA` (South and Central America), `EU` (Europe), `OC` (Oceania), `AS` (Asia), `AF` (Africa), `AN` (Antarctica). | AS |
| country | The country of your Ethereum full node. | China |


